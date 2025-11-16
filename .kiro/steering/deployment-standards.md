# Deployment and Pipeline Standards

## Environment Strategy

### Environment Tiers
1. **Local**: Developer workstations
2. **Dev**: Shared development environment
3. **UAT**: User Acceptance Testing
4. **Prod**: Production environment

### Environment Characteristics
- Each environment has isolated database
- Separate configuration per environment
- No shared resources between UAT and Prod
- Dev environment mirrors production architecture

## CI/CD Pipeline Architecture

### Pipeline Stages

#### 1. Build Stage
- Restore dependencies
- Compile code (.NET 9.0)
- Build React application
- Run linters (ESLint, StyleCop)
- Type checking (TypeScript)
- Generate build artifacts

#### 2. Test Stage
- Run unit tests
- Run integration tests
- Generate code coverage reports
- Fail build if coverage < 70%
- Run security scans (OWASP dependency check)

#### 3. Database Migration Stage
- Validate Liquibase changesets
- Run migrations in target environment
- Verify migration success
- Rollback on failure

#### 4. Deploy Stage
- Deploy API to application server
- Deploy UI to web server/CDN
- Update configuration
- Run smoke tests
- Health check verification

#### 5. Post-Deployment Stage
- Run E2E tests
- Monitor error rates
- Verify key metrics
- Send deployment notifications

### Pipeline Tools
- **CI/CD Platform**: Azure DevOps, GitHub Actions, or GitLab CI
- **Build**: .NET CLI, npm/yarn
- **Testing**: xUnit, Jest, Playwright
- **Database**: Liquibase
- **Containerization**: Docker (optional)

## Development Pipeline

### Trigger
- Automatic on push to `develop` branch
- Manual trigger available

### Steps
1. Checkout code
2. Build application
3. Run unit tests
4. Run linters
5. Deploy to Dev environment
6. Run integration tests
7. Notify team of results

### Dev Environment
- Automatically deployed on successful build
- Latest code always available
- Used for integration testing
- May be unstable

## UAT Promotion Pipeline

### Trigger
- Manual approval required
- Triggered from successful Dev deployment
- Requires QA sign-off

### Steps
1. Create release candidate
2. Run full test suite
3. Apply database migrations (Liquibase)
4. Deploy API to UAT
5. Deploy UI to UAT
6. Run smoke tests
7. Run E2E tests
8. Notify stakeholders

### UAT Environment
- Stable environment for user testing
- Production-like configuration
- Sanitized production data (optional)
- Requires approval for deployment

### UAT Approval Process
- QA team validates functionality
- Business stakeholders review features
- Security review for sensitive changes
- Sign-off required before production

## Production Promotion Pipeline

### Trigger
- Manual approval required
- Requires UAT sign-off
- Scheduled deployment window preferred

### Pre-Deployment Checklist
- [ ] UAT testing completed and approved
- [ ] Database backup completed
- [ ] Rollback plan documented
- [ ] Stakeholders notified
- [ ] Maintenance window scheduled (if needed)
- [ ] On-call team alerted

### Deployment Steps
1. Enable maintenance mode (if required)
2. Create production database backup
3. Apply database migrations (Liquibase)
4. Deploy API (blue-green or rolling deployment)
5. Deploy UI
6. Run smoke tests
7. Verify health checks
8. Monitor error rates for 30 minutes
9. Disable maintenance mode
10. Send deployment notification

### Production Environment
- High availability configuration
- Automated monitoring and alerting
- Strict access controls
- Comprehensive logging

### Rollback Procedure
1. Identify issue requiring rollback
2. Execute rollback plan
3. Restore database from backup (if needed)
4. Revert application deployment
5. Verify system stability
6. Notify stakeholders
7. Post-mortem analysis

## Database Deployment with Liquibase

### Liquibase Configuration
```yaml
# liquibase.properties
changeLogFile: db/changelog/db.changelog-master.xml
url: jdbc:postgresql://localhost:5432/mydb
username: ${DB_USER}
password: ${DB_PASSWORD}
driver: org.postgresql.Driver
```

### Deployment Process
1. Validate changesets: `liquibase validate`
2. Generate SQL preview: `liquibase updateSQL`
3. Review generated SQL
4. Apply migrations: `liquibase update`
5. Tag release: `liquibase tag v1.0.0`
6. Verify migration: Check databasechangelog table

### Rollback Process
```bash
# Rollback last changeset
liquibase rollback-count 1

# Rollback to specific tag
liquibase rollback v1.0.0

# Rollback to specific date
liquibase rollback-to-date 2024-11-15
```

### Best Practices
- Always test migrations in Dev and UAT first
- Include rollback scripts in changesets
- Never modify deployed changesets
- Use tags for release versions
- Monitor migration execution time

## Database Restore for Production Support

### Restore Scenarios
1. **Bug Investigation**: Need production data to reproduce issue
2. **Data Recovery**: Accidental data deletion
3. **Performance Analysis**: Query optimization with real data
4. **Compliance Audit**: Historical data review

### Restore Process
1. Request approval from data owner
2. Create support database instance
3. Restore from latest backup
4. Apply PII masking scripts
5. Grant temporary access to support team
6. Document access in audit log
7. Destroy support database after resolution

### PII Masking for Support Database
```sql
-- Mask email addresses
UPDATE users SET email = CONCAT('user', id, '@example.com');

-- Mask phone numbers
UPDATE users SET phone = '555-0100';

-- Mask names
UPDATE users SET 
    first_name = CONCAT('FirstName', id),
    last_name = CONCAT('LastName', id);

-- Mask addresses
UPDATE addresses SET 
    street = '123 Main St',
    city = 'Anytown',
    postal_code = '00000';
```

### Access Controls
- Time-limited access (24-48 hours)
- Read-only access preferred
- Audit all queries executed
- Require justification for access
- Automatic access revocation

## Deployment Strategies

### Blue-Green Deployment
- Maintain two identical production environments
- Deploy to inactive environment (green)
- Test green environment
- Switch traffic to green
- Keep blue as instant rollback option

### Rolling Deployment
- Deploy to subset of servers
- Monitor for issues
- Gradually roll out to all servers
- Automatic rollback on error threshold

### Canary Deployment
- Deploy to small percentage of users (5-10%)
- Monitor metrics closely
- Gradually increase traffic
- Full rollout if metrics are healthy

## Monitoring and Alerting

### Key Metrics
- Application uptime
- Response time (p50, p95, p99)
- Error rate
- Database connection pool usage
- CPU and memory utilization
- Disk space

### Alerting Thresholds
- **Critical**: Error rate > 5%, Response time > 2s
- **Warning**: Error rate > 1%, Response time > 1s
- **Info**: Deployment completed, backup completed

### Post-Deployment Monitoring
- Monitor for 30 minutes after deployment
- Compare metrics to baseline
- Watch for error spikes
- Verify key user journeys
- Check database performance

## Configuration Management

### Environment Variables
- Store configuration in environment variables
- Never commit secrets to source control
- Use Azure Key Vault or similar for secrets
- Different secrets per environment

### Configuration Files
```
config/
├── appsettings.json              # Base configuration
├── appsettings.Development.json  # Dev overrides
├── appsettings.UAT.json          # UAT overrides
└── appsettings.Production.json   # Prod overrides
```

### Secret Management
- Rotate secrets quarterly
- Use managed identities when possible
- Audit secret access
- Revoke compromised secrets immediately

## Deployment Documentation

### Required Documentation
- Deployment runbook
- Rollback procedures
- Configuration changes
- Database migration details
- Known issues and workarounds
- Contact information for on-call team

### Deployment Notification
- Notify stakeholders before deployment
- Include deployment window
- List changes being deployed
- Provide rollback plan
- Share status updates during deployment

## Quality Gates

### Pre-Deployment Gates
- All tests passing
- Code review completed
- Security scan passed
- Performance benchmarks met
- Documentation updated

### Post-Deployment Gates
- Smoke tests passed
- Health checks green
- Error rate within threshold
- Key metrics stable
- User acceptance confirmed (UAT)

## Compliance and Audit

### Deployment Audit Trail
- Log all deployments with timestamp
- Record who approved deployment
- Document what was deployed
- Track configuration changes
- Maintain deployment history

### Compliance Requirements
- Maintain separation of duties
- Require approvals for production
- Document change management process
- Retain deployment logs per policy
- Regular audit of deployment practices
