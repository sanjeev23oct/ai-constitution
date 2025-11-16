# Database Development Standards

## Technology Stack

- **Database**: PostgreSQL 15+
- **Migration Tool**: Liquibase
- **Development Approach**: Stored Procedures and Functions
- **Local Setup**: Required for all developers

## Database Architecture

### Schema Organization
```
database/
├── schemas/
│   ├── public/          # Default schema
│   ├── audit/           # Audit tables
│   └── config/          # Configuration tables
├── functions/           # PostgreSQL functions
├── procedures/          # Stored procedures
├── triggers/            # Database triggers
└── migrations/          # Liquibase changesets
```

### Naming Conventions
- Tables: snake_case, plural (e.g., `user_accounts`)
- Columns: snake_case (e.g., `first_name`)
- Primary Keys: `id` or `{table_name}_id`
- Foreign Keys: `{referenced_table}_id`
- Indexes: `idx_{table}_{column(s)}`
- Functions: snake_case with verb prefix (e.g., `get_user_by_email`)
- Stored Procedures: `sp_{action}_{entity}` (e.g., `sp_create_user`)

## Development Approach

### Stored Procedures and Functions
- **All business logic** must be in stored procedures/functions
- Use functions for read operations (SELECT)
- Use procedures for write operations (INSERT, UPDATE, DELETE)
- Keep procedures focused and single-purpose
- Return result sets or status codes

### Example Stored Procedure
```sql
CREATE OR REPLACE PROCEDURE sp_create_user(
    p_email VARCHAR(255),
    p_first_name VARCHAR(100),
    p_last_name VARCHAR(100),
    OUT p_user_id INTEGER,
    OUT p_status_code INTEGER,
    OUT p_message TEXT
)
LANGUAGE plpgsql
AS $$
BEGIN
    -- Validation
    IF p_email IS NULL OR p_email = '' THEN
        p_status_code := 400;
        p_message := 'Email is required';
        RETURN;
    END IF;
    
    -- Insert user
    INSERT INTO users (email, first_name, last_name, created_at)
    VALUES (p_email, p_first_name, p_last_name, NOW())
    RETURNING id INTO p_user_id;
    
    p_status_code := 201;
    p_message := 'User created successfully';
    
EXCEPTION
    WHEN unique_violation THEN
        p_status_code := 409;
        p_message := 'Email already exists';
    WHEN OTHERS THEN
        p_status_code := 500;
        p_message := 'Internal server error';
END;
$$;
```

### Example Function
```sql
CREATE OR REPLACE FUNCTION get_user_by_id(p_user_id INTEGER)
RETURNS TABLE (
    id INTEGER,
    email VARCHAR(255),
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    created_at TIMESTAMP
)
LANGUAGE plpgsql
AS $$
BEGIN
    RETURN QUERY
    SELECT u.id, u.email, u.first_name, u.last_name, u.created_at
    FROM users u
    WHERE u.id = p_user_id AND u.is_deleted = FALSE;
END;
$$;
```

## Data Modeling Standards

### Table Design
- Every table must have a primary key
- Use SERIAL or BIGSERIAL for auto-incrementing IDs
- Include audit columns: `created_at`, `updated_at`, `created_by`, `updated_by`
- Implement soft deletes with `is_deleted` flag
- Use appropriate data types (avoid TEXT when VARCHAR is sufficient)

### Standard Table Template
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    is_deleted BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    created_by INTEGER REFERENCES users(id),
    updated_by INTEGER REFERENCES users(id)
);
```

### Indexing Strategy
- Index all foreign keys
- Index columns used in WHERE clauses frequently
- Create composite indexes for multi-column queries
- Monitor and optimize slow queries
- Use partial indexes for filtered queries

```sql
CREATE INDEX idx_users_email ON users(email) WHERE is_deleted = FALSE;
CREATE INDEX idx_orders_user_date ON orders(user_id, order_date);
```

## Migration Management with Liquibase

### Changeset Structure
- One changeset per logical change
- Use descriptive IDs: `YYYYMMDD-HHMM-description`
- Include rollback statements when possible
- Tag releases for easy rollback

### Changeset Example
```xml
<changeSet id="20241116-1200-create-users-table" author="developer">
    <createTable tableName="users">
        <column name="id" type="SERIAL">
            <constraints primaryKey="true"/>
        </column>
        <column name="email" type="VARCHAR(255)">
            <constraints nullable="false" unique="true"/>
        </column>
        <column name="created_at" type="TIMESTAMP" defaultValueComputed="NOW()"/>
    </createTable>
    
    <rollback>
        <dropTable tableName="users"/>
    </rollback>
</changeSet>
```

### Migration Best Practices
- Never modify existing changesets after deployment
- Test migrations on local database first
- Always include rollback scripts
- Use preconditions to prevent duplicate execution
- Keep changesets small and focused

## Local Database Setup

### Requirements
- Every developer must have local PostgreSQL instance
- Use Docker for consistent environment
- Seed data scripts for development
- Automated setup script for new developers

### Docker Compose Example
```yaml
version: '3.8'
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: myapp_dev
      POSTGRES_USER: devuser
      POSTGRES_PASSWORD: devpass
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./scripts/seed:/docker-entrypoint-initdb.d
```

## Security Standards

### PII Protection
- Identify and document all PII columns
- Implement column-level encryption for sensitive data
- Use PostgreSQL's pgcrypto extension for encryption
- Restrict access to PII columns with row-level security
- Mask PII in non-production environments

### Access Control
- Use role-based access control (RBAC)
- Principle of least privilege
- Separate roles for read-only and read-write access
- Application uses dedicated service account
- No direct database access for developers in production

### Example Roles
```sql
CREATE ROLE app_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO app_readonly;

CREATE ROLE app_readwrite;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_readwrite;
GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA public TO app_readwrite;
```

## Performance Standards

### Query Performance
- All queries must complete in < 500ms
- Use EXPLAIN ANALYZE for query optimization
- Avoid N+1 queries
- Use connection pooling (PgBouncer)
- Monitor slow query log

### Optimization Techniques
- Use appropriate indexes
- Avoid SELECT *; specify columns
- Use LIMIT for large result sets
- Implement pagination at database level
- Use materialized views for complex aggregations

## Audit and Logging

### Audit Trail
- Log all data modifications (INSERT, UPDATE, DELETE)
- Store audit logs in separate schema
- Include user ID, timestamp, and changed values
- Implement using triggers or application-level logging

### Audit Table Example
```sql
CREATE TABLE audit.user_changes (
    audit_id SERIAL PRIMARY KEY,
    table_name VARCHAR(50),
    record_id INTEGER,
    action VARCHAR(10),
    old_values JSONB,
    new_values JSONB,
    changed_by INTEGER,
    changed_at TIMESTAMP DEFAULT NOW()
);
```

## Testing Standards

### Database Testing
- Write unit tests for stored procedures and functions
- Use pgTAP for database unit testing
- Test edge cases and error conditions
- Verify constraints and triggers
- Test rollback scenarios

### Test Data Management
- Maintain seed data scripts
- Use transactions for test isolation
- Clean up test data after tests
- Never use production data in tests

## Backup and Recovery

### Backup Strategy
- Daily full backups
- Point-in-time recovery enabled
- Test restore procedures quarterly
- Document recovery procedures
- Store backups in separate location

### Production Support
- Maintain sanitized production backup for support
- Document restore process for prod support scenarios
- Implement PII masking for support databases
- Require approval for production data access

## Documentation

### Required Documentation
- Entity-Relationship Diagrams (ERD)
- Data dictionary for all tables
- Stored procedure/function documentation
- Migration history and rationale
- Performance tuning notes

### Code Comments
- Document complex business logic
- Explain non-obvious constraints
- Include examples for stored procedures
- Document expected input/output

## Deployment Process

### Environment Progression
1. **Local**: Developer testing
2. **Dev**: Integration testing
3. **UAT**: User acceptance testing
4. **Prod**: Production deployment

### Deployment Checklist
- [ ] Liquibase changesets reviewed
- [ ] Rollback scripts tested
- [ ] Performance impact assessed
- [ ] Backup completed before deployment
- [ ] Migration tested in UAT
- [ ] Documentation updated
