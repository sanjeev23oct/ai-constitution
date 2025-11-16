# Quality Assurance and Testing Standards

## Testing Philosophy

- **Test Early, Test Often**: Integrate testing throughout development
- **Shift Left**: Catch issues as early as possible
- **Automation First**: Automate repetitive testing tasks
- **Quality is Everyone's Responsibility**: Not just QA team

## Testing Pyramid

### Unit Tests (70% of tests)
- Test individual functions and methods
- Fast execution (< 1 second per test)
- No external dependencies
- High code coverage target: 80%+

### Integration Tests (20% of tests)
- Test component interactions
- Include database and API calls
- Use test databases
- Moderate execution time

### E2E Tests (10% of tests)
- Test complete user workflows
- Simulate real user behavior
- Run in browser environment
- Slower execution acceptable

## Unit Testing Standards

### .NET Unit Testing
- **Framework**: xUnit
- **Mocking**: Moq or NSubstitute
- **Assertions**: FluentAssertions

### Test Structure (AAA Pattern)
```csharp
[Fact]
public async Task CreateUser_ValidInput_ReturnsCreatedUser()
{
    // Arrange
    var userDto = new CreateUserDto 
    { 
        Email = "test@example.com",
        FirstName = "John",
        LastName = "Doe"
    };
    var mockRepo = new Mock<IUserRepository>();
    mockRepo.Setup(r => r.CreateAsync(It.IsAny<User>()))
            .ReturnsAsync(1);
    var service = new UserService(mockRepo.Object);
    
    // Act
    var result = await service.CreateAsync(userDto);
    
    // Assert
    result.Should().NotBeNull();
    result.Email.Should().Be(userDto.Email);
    mockRepo.Verify(r => r.CreateAsync(It.IsAny<User>()), Times.Once);
}
```

### React Unit Testing
- **Framework**: Jest + React Testing Library
- **Approach**: Test user behavior, not implementation

```typescript
test('displays user name when loaded', async () => {
  // Arrange
  const mockUser = { id: 1, name: 'John Doe' };
  jest.spyOn(userService, 'getUser').mockResolvedValue(mockUser);
  
  // Act
  render(<UserProfile userId={1} />);
  
  // Assert
  expect(await screen.findByText('John Doe')).toBeInTheDocument();
});
```

### Unit Test Best Practices
- One assertion per test (when possible)
- Test edge cases and error conditions
- Use descriptive test names
- Keep tests independent
- Avoid test interdependencies
- Mock external dependencies

## Integration Testing Standards

### API Integration Tests
- Test complete API endpoints
- Use TestServer or similar
- Test with real database (test instance)
- Verify authentication and authorization
- Test error responses

### Example
```csharp
[Fact]
public async Task GetUser_ValidId_ReturnsUser()
{
    // Arrange
    var client = _factory.CreateClient();
    var userId = await SeedTestUser();
    
    // Act
    var response = await client.GetAsync($"/api/v1/users/{userId}");
    
    // Assert
    response.StatusCode.Should().Be(HttpStatusCode.OK);
    var user = await response.Content.ReadAsAsync<UserDto>();
    user.Id.Should().Be(userId);
}
```

### Database Integration Tests
- Test stored procedures and functions
- Use pgTAP for PostgreSQL testing
- Test transactions and rollbacks
- Verify constraints and triggers

## End-to-End Testing Standards

### E2E Framework
- **Tool**: Playwright or Cypress
- **Scope**: Critical user journeys only
- **Execution**: Run in CI/CD pipeline

### Critical User Journeys
1. User registration and login
2. Core business workflows
3. Payment processing (if applicable)
4. Data submission and retrieval
5. Admin functions

### E2E Test Example (Playwright)
```typescript
test('user can login and view dashboard', async ({ page }) => {
  // Navigate to login
  await page.goto('/login');
  
  // Enter credentials
  await page.fill('[name="email"]', 'test@example.com');
  await page.fill('[name="password"]', 'password123');
  await page.click('button[type="submit"]');
  
  // Verify dashboard loads
  await expect(page).toHaveURL('/dashboard');
  await expect(page.locator('h1')).toContainText('Dashboard');
});
```

### E2E Best Practices
- Use data-testid attributes for selectors
- Clean up test data after execution
- Run tests in isolated environment
- Use realistic test data
- Handle async operations properly

## Test Data Management

### Test Data Strategy
- Use factories or builders for test data
- Maintain seed data scripts
- Clean up after tests
- Never use production data in tests

### Test Data Factory Example
```csharp
public class UserFactory
{
    public static User CreateValidUser(string email = null)
    {
        return new User
        {
            Email = email ?? $"test{Guid.NewGuid()}@example.com",
            FirstName = "Test",
            LastName = "User",
            IsActive = true
        };
    }
}
```

## Code Coverage Standards

### Coverage Targets
- **Overall**: 80% minimum
- **Critical paths**: 95% minimum
- **New code**: 90% minimum
- **UI components**: 70% minimum

### Coverage Tools
- .NET: Coverlet + ReportGenerator
- React: Jest built-in coverage

### Coverage Reports
- Generate coverage reports in CI/CD
- Fail build if coverage drops below threshold
- Review coverage trends over time
- Focus on meaningful coverage, not just numbers

## Performance Testing

### Load Testing
- Test expected peak load + 50%
- Identify bottlenecks
- Measure response times under load
- Test database performance

### Performance Benchmarks
- API response time: < 500ms (p95)
- Page load time: < 2s
- Database queries: < 100ms
- Concurrent users: Support 1000+ users

### Tools
- **Load Testing**: k6, JMeter, or Artillery
- **Profiling**: dotTrace, Chrome DevTools

## Security Testing

### Automated Security Scans
- OWASP dependency check in CI/CD
- Static Application Security Testing (SAST)
- Dynamic Application Security Testing (DAST)
- Container scanning (if using Docker)

### Security Test Cases
- SQL injection attempts
- XSS attack vectors
- CSRF protection
- Authentication bypass attempts
- Authorization checks
- Input validation

## Penetration Testing

### PEN Testing Schedule
- Before initial production launch
- Annually for production systems
- After major security-related changes
- After significant architecture changes

### PEN Testing Scope
- External penetration testing
- Internal network testing (if applicable)
- Web application testing
- API security testing
- Authentication and session management
- Data encryption verification

### PEN Testing Process
1. **Planning**: Define scope and objectives
2. **Reconnaissance**: Information gathering
3. **Scanning**: Identify vulnerabilities
4. **Exploitation**: Attempt to exploit vulnerabilities
5. **Reporting**: Document findings
6. **Remediation**: Fix identified issues
7. **Retesting**: Verify fixes

### Remediation Timeline
- **Critical**: Fix within 7 days
- **High**: Fix within 30 days
- **Medium**: Fix within 90 days
- **Low**: Fix in next release cycle

## Test Automation

### Automation Strategy
- Automate all regression tests
- Automate smoke tests
- Automate API tests
- Automate critical E2E flows

### CI/CD Integration
- Run unit tests on every commit
- Run integration tests on PR
- Run E2E tests before deployment
- Run security scans daily

### Test Execution
- Parallel test execution for speed
- Retry flaky tests (max 2 retries)
- Fail fast on critical failures
- Generate test reports

## Manual Testing

### When to Use Manual Testing
- Exploratory testing
- Usability testing
- Visual design verification
- Complex user workflows
- Edge cases not covered by automation

### Manual Test Cases
- Document test cases in test management tool
- Include preconditions and expected results
- Maintain test case repository
- Review and update regularly

## Bug Management

### Bug Reporting
- Use standardized bug template
- Include steps to reproduce
- Attach screenshots/videos
- Specify environment
- Assign severity and priority

### Bug Severity Levels
- **Critical**: System crash, data loss, security breach
- **High**: Major functionality broken
- **Medium**: Feature partially working
- **Low**: Minor issue, cosmetic

### Bug Lifecycle
1. New
2. Assigned
3. In Progress
4. Fixed
5. Ready for Testing
6. Verified
7. Closed

## Quality Metrics

### Track and Monitor
- Test pass rate
- Code coverage percentage
- Bug detection rate
- Bug fix time
- Test execution time
- Flaky test rate

### Quality Gates
- All tests must pass
- Code coverage > 80%
- No critical or high severity bugs
- Security scan passed
- Performance benchmarks met

## Testing Documentation

### Required Documentation
- Test plan for major features
- Test cases for critical functionality
- Test data requirements
- Environment setup instructions
- Known issues and limitations

### Test Reports
- Daily test execution summary
- Weekly quality metrics report
- Release testing report
- PEN testing report

## Continuous Improvement

### Retrospectives
- Review testing effectiveness
- Identify gaps in test coverage
- Discuss flaky tests
- Share lessons learned

### Test Maintenance
- Remove obsolete tests
- Update tests for changed requirements
- Refactor duplicate test code
- Improve test performance
