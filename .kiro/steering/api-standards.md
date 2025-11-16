# API Development Standards

## Technology Stack

- **Framework**: .NET 9.0
- **API Style**: RESTful with OpenAPI/Swagger documentation
- **Language**: C# 12+
- **Authentication**: Authentik integration
- **ORM**: Entity Framework Core (minimal usage, prefer stored procedures)

## Project Structure

### Solution Organization
```
src/
├── API/                    # Web API project
│   ├── Controllers/
│   ├── Middleware/
│   ├── Filters/
│   └── Program.cs
├── Application/            # Business logic layer
│   ├── Services/
│   ├── Interfaces/
│   ├── DTOs/
│   └── Validators/
├── Domain/                 # Domain models and interfaces
│   ├── Entities/
│   └── Interfaces/
├── Infrastructure/         # Data access and external services
│   ├── Data/
│   ├── Repositories/
│   └── ExternalServices/
└── Tests/
    ├── UnitTests/
    ├── IntegrationTests/
    └── E2ETests/
```

## API Design Principles

### RESTful Conventions
- Use proper HTTP verbs: GET, POST, PUT, PATCH, DELETE
- Use plural nouns for resource endpoints (e.g., `/api/users`)
- Use hierarchical structure for relationships (e.g., `/api/users/{id}/orders`)
- Version APIs in URL (e.g., `/api/v1/users`)

### Endpoint Naming
```
GET    /api/v1/users           # List all users
GET    /api/v1/users/{id}      # Get specific user
POST   /api/v1/users           # Create user
PUT    /api/v1/users/{id}      # Update user (full)
PATCH  /api/v1/users/{id}      # Update user (partial)
DELETE /api/v1/users/{id}      # Delete user
```

### Response Standards
- Use standard HTTP status codes
  - 200 OK: Successful GET, PUT, PATCH
  - 201 Created: Successful POST
  - 204 No Content: Successful DELETE
  - 400 Bad Request: Validation errors
  - 401 Unauthorized: Missing/invalid authentication
  - 403 Forbidden: Insufficient permissions
  - 404 Not Found: Resource doesn't exist
  - 500 Internal Server Error: Server errors

### Response Format
```json
{
  "success": true,
  "data": { },
  "message": "Operation completed successfully",
  "errors": null
}
```

For errors:
```json
{
  "success": false,
  "data": null,
  "message": "Validation failed",
  "errors": [
    {
      "field": "email",
      "message": "Invalid email format"
    }
  ]
}
```

## Authentication and Authorization

### Authentik Integration
- Use OAuth 2.0 / OpenID Connect flow
- Validate JWT tokens on every request
- Implement token refresh mechanism
- Store tokens securely (never in logs)

### Authorization
- Use policy-based authorization in .NET
- Implement role-based access control (RBAC)
- Use claims for fine-grained permissions
- Apply `[Authorize]` attribute at controller/action level

### Example
```csharp
[Authorize(Policy = "RequireAdminRole")]
[HttpDelete("api/v1/users/{id}")]
public async Task<IActionResult> DeleteUser(int id)
{
    // Implementation
}
```

## Data Access Layer

### Database Interaction
- **Primary Approach**: Stored Procedures and Functions
- Use Entity Framework Core only for simple CRUD operations
- Complex business logic must be in stored procedures
- Use Dapper for stored procedure execution when EF Core is insufficient

### Repository Pattern
```csharp
public interface IUserRepository
{
    Task<User> GetByIdAsync(int id);
    Task<IEnumerable<User>> GetAllAsync();
    Task<int> CreateAsync(User user);
    Task UpdateAsync(User user);
    Task DeleteAsync(int id);
}
```

### Stored Procedure Execution
```csharp
public async Task<User> GetUserByIdAsync(int userId)
{
    var parameters = new[]
    {
        new SqlParameter("@UserId", userId)
    };
    
    return await _context.Users
        .FromSqlRaw("EXEC sp_GetUserById @UserId", parameters)
        .FirstOrDefaultAsync();
}
```

## Input Validation

### DTO Validation
- Use FluentValidation for complex validation rules
- Use Data Annotations for simple validations
- Validate all inputs at API boundary
- Return detailed validation errors

### Example with FluentValidation
```csharp
public class CreateUserValidator : AbstractValidator<CreateUserDto>
{
    public CreateUserValidator()
    {
        RuleFor(x => x.Email)
            .NotEmpty()
            .EmailAddress();
            
        RuleFor(x => x.Password)
            .MinimumLength(8)
            .Matches(@"[A-Z]").WithMessage("Password must contain uppercase")
            .Matches(@"[0-9]").WithMessage("Password must contain number");
    }
}
```

## Error Handling

### Global Exception Handling
- Implement global exception middleware
- Log all exceptions with correlation IDs
- Never expose stack traces to clients in production
- Return user-friendly error messages

### Exception Middleware Example
```csharp
public class ExceptionMiddleware
{
    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Unhandled exception");
            await HandleExceptionAsync(context, ex);
        }
    }
}
```

## Logging Standards

### Structured Logging
- Use Serilog or NLog for structured logging
- Include correlation IDs in all logs
- Log levels: Trace, Debug, Information, Warning, Error, Critical
- Never log sensitive data (passwords, tokens, PII)

### What to Log
- **Information**: API requests/responses (sanitized)
- **Warning**: Validation failures, business rule violations
- **Error**: Exceptions, failed operations
- **Critical**: System failures, data corruption

### Log Format
```csharp
_logger.LogInformation(
    "User {UserId} accessed resource {ResourceId} at {Timestamp}",
    userId, resourceId, DateTime.UtcNow
);
```

## Performance Standards

### Response Time Targets
- Simple queries: < 100ms
- Complex queries: < 500ms
- Batch operations: < 2s
- File uploads: < 5s (for reasonable file sizes)

### Optimization Techniques
- Use async/await for all I/O operations
- Implement caching for frequently accessed data (Redis)
- Use pagination for list endpoints (default page size: 20)
- Implement database connection pooling
- Use compiled queries for repeated operations

### Pagination Standard
```csharp
[HttpGet("api/v1/users")]
public async Task<IActionResult> GetUsers(
    [FromQuery] int page = 1,
    [FromQuery] int pageSize = 20)
{
    var users = await _userService.GetPagedAsync(page, pageSize);
    return Ok(new
    {
        data = users,
        page,
        pageSize,
        totalCount = users.TotalCount
    });
}
```

## Security Standards

### PII Protection
- Identify all PII fields in data models
- Never log PII data
- Implement field-level encryption for sensitive data
- Use data masking in non-production environments
- Validate PII handling in code reviews

### API Security
- Implement rate limiting (e.g., 100 requests/minute per user)
- Use HTTPS only (enforce in production)
- Implement CORS with whitelist approach
- Validate and sanitize all inputs
- Use parameterized queries (prevent SQL injection)
- Implement request size limits

### CORS Configuration
```csharp
services.AddCors(options =>
{
    options.AddPolicy("AllowedOrigins", builder =>
    {
        builder.WithOrigins(Configuration["AllowedOrigins"])
               .AllowAnyMethod()
               .AllowAnyHeader();
    });
});
```

## Testing Standards

### Unit Testing
- Use xUnit as testing framework
- Use Moq for mocking dependencies
- Aim for 80%+ code coverage
- Test business logic thoroughly
- Follow AAA pattern (Arrange, Act, Assert)

### Integration Testing
- Test API endpoints with TestServer
- Use in-memory database or test database
- Test authentication and authorization
- Test error scenarios

### Example Unit Test
```csharp
[Fact]
public async Task CreateUser_ValidInput_ReturnsCreatedUser()
{
    // Arrange
    var userDto = new CreateUserDto { Email = "test@example.com" };
    _mockRepository.Setup(r => r.CreateAsync(It.IsAny<User>()))
                   .ReturnsAsync(1);
    
    // Act
    var result = await _userService.CreateAsync(userDto);
    
    // Assert
    Assert.NotNull(result);
    Assert.Equal(userDto.Email, result.Email);
}
```

## Documentation

### OpenAPI/Swagger
- Enable Swagger in development and UAT
- Document all endpoints with XML comments
- Include request/response examples
- Document authentication requirements
- Version API documentation

### XML Documentation
```csharp
/// <summary>
/// Creates a new user
/// </summary>
/// <param name="userDto">User creation data</param>
/// <returns>Created user with ID</returns>
/// <response code="201">User created successfully</response>
/// <response code="400">Invalid input data</response>
[HttpPost("api/v1/users")]
[ProducesResponseType(typeof(UserDto), StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public async Task<IActionResult> CreateUser([FromBody] CreateUserDto userDto)
{
    // Implementation
}
```

## Configuration Management

### Environment-Specific Settings
- Use appsettings.json for base configuration
- Override with appsettings.{Environment}.json
- Use environment variables for secrets
- Never commit secrets to source control
- Use Azure Key Vault or similar for production secrets

### Configuration Structure
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=mydb;..."
  },
  "Authentik": {
    "Authority": "https://auth.example.com",
    "ClientId": "api-client"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

## Deployment Standards

### Build Configuration
- Use Release configuration for all deployments
- Enable code optimization
- Remove debug symbols in production
- Include health check endpoints

### Health Checks
```csharp
builder.Services.AddHealthChecks()
    .AddNpgSql(connectionString)
    .AddUrlGroup(new Uri("https://auth.example.com"), "Authentik");

app.MapHealthChecks("/health");
```

## Code Quality

### Code Analysis
- Enable nullable reference types
- Use StyleCop or similar for code style
- Run code analysis in CI/CD pipeline
- Zero warnings policy for production code

### Code Review Checklist
- [ ] Follows RESTful conventions
- [ ] Proper error handling implemented
- [ ] Input validation present
- [ ] No PII in logs
- [ ] Tests included
- [ ] Documentation updated
- [ ] Security considerations addressed
