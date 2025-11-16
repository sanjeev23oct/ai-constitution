# Logging Standards

## Logging Philosophy

- **Observability First**: Logs enable understanding system behavior
- **Structured Logging**: Use structured formats for easy parsing
- **Security Conscious**: Never log sensitive data
- **Performance Aware**: Logging should not impact performance
- **Actionable**: Logs should help diagnose and resolve issues

## Technology Stack

### Backend (.NET)
- **Framework**: Serilog or NLog
- **Sinks**: Console, File, Application Insights/ELK
- **Format**: JSON structured logging

### Frontend (React)
- **Framework**: Custom logger or winston
- **Transport**: Console, Remote logging service
- **Error Tracking**: Sentry, Rollbar, or similar

## Log Levels

### Level Definitions
- **Trace**: Very detailed diagnostic information (disabled in production)
- **Debug**: Detailed information for debugging (disabled in production)
- **Information**: General informational messages
- **Warning**: Potentially harmful situations
- **Error**: Error events that might still allow the application to continue
- **Critical**: Very severe error events that might cause the application to abort

### When to Use Each Level

**Trace**
- Method entry/exit
- Variable values during execution
- Detailed flow information

**Debug**
- Diagnostic information
- Intermediate calculation results
- Configuration values

**Information**
- Application startup/shutdown
- Significant business events
- User actions (login, logout)
- API requests/responses (sanitized)

**Warning**
- Deprecated API usage
- Recoverable errors
- Performance degradation
- Configuration issues

**Error**
- Exceptions caught and handled
- Failed operations
- Integration failures
- Validation errors

**Critical**
- Application crashes
- Data corruption
- Security breaches
- System-wide failures

## Structured Logging

### Log Format
Use JSON format with consistent structure:
```json
{
  "timestamp": "2024-11-16T10:30:00.000Z",
  "level": "Information",
  "message": "User logged in successfully",
  "correlationId": "abc-123-def-456",
  "userId": 12345,
  "ipAddress": "192.168.1.1",
  "userAgent": "Mozilla/5.0...",
  "duration": 150,
  "environment": "Production"
}
```

### .NET Structured Logging Example
```csharp
_logger.LogInformation(
    "User {UserId} performed {Action} on {Resource} at {Timestamp}",
    userId,
    action,
    resourceId,
    DateTime.UtcNow
);
```

### React Structured Logging Example
```typescript
logger.info('User action completed', {
  userId: user.id,
  action: 'submit_form',
  formId: 'contact-form',
  duration: 1250,
  correlationId: getCorrelationId()
});
```

## Correlation IDs

### Purpose
- Track requests across services
- Correlate frontend and backend logs
- Debug distributed systems

### Implementation
- Generate correlation ID in frontend
- Pass in HTTP header: `X-Correlation-ID`
- Include in all log entries
- Return in API responses

### Example
```csharp
// Middleware to extract correlation ID
public async Task InvokeAsync(HttpContext context)
{
    var correlationId = context.Request.Headers["X-Correlation-ID"]
        .FirstOrDefault() ?? Guid.NewGuid().ToString();
    
    using (_logger.BeginScope(new Dictionary<string, object>
    {
        ["CorrelationId"] = correlationId
    }))
    {
        context.Response.Headers.Add("X-Correlation-ID", correlationId);
        await _next(context);
    }
}
```

## What to Log

### API Requests
```csharp
_logger.LogInformation(
    "API Request: {Method} {Path} from {IpAddress}",
    context.Request.Method,
    context.Request.Path,
    context.Connection.RemoteIpAddress
);
```

### API Responses
```csharp
_logger.LogInformation(
    "API Response: {StatusCode} for {Method} {Path} in {Duration}ms",
    context.Response.StatusCode,
    context.Request.Method,
    context.Request.Path,
    stopwatch.ElapsedMilliseconds
);
```

### Business Events
```csharp
_logger.LogInformation(
    "Order {OrderId} created by user {UserId} with total {Total}",
    order.Id,
    userId,
    order.Total
);
```

### Authentication Events
```csharp
// Successful login
_logger.LogInformation(
    "User {UserId} logged in successfully from {IpAddress}",
    userId,
    ipAddress
);

// Failed login
_logger.LogWarning(
    "Failed login attempt for email {Email} from {IpAddress}",
    email,
    ipAddress
);
```

### Database Operations
```csharp
_logger.LogInformation(
    "Executing stored procedure {ProcedureName} with parameters {Parameters}",
    procedureName,
    parameters
);
```

### Errors and Exceptions
```csharp
try
{
    // Operation
}
catch (Exception ex)
{
    _logger.LogError(
        ex,
        "Failed to process order {OrderId} for user {UserId}",
        orderId,
        userId
    );
    throw;
}
```

## What NOT to Log

### Sensitive Data (NEVER LOG)
- Passwords or password hashes
- Authentication tokens (JWT, API keys)
- Credit card numbers
- Social Security Numbers
- Personal Identifiable Information (PII)
  - Full names (use user ID instead)
  - Email addresses (mask or use ID)
  - Phone numbers
  - Addresses
- Encryption keys
- Security questions/answers
- Session IDs
- Full request/response bodies (may contain sensitive data)

### Example: Safe Logging
```csharp
// BAD - Logs sensitive data
_logger.LogInformation("User logged in: {Email}, {Password}", email, password);

// GOOD - Logs only necessary info
_logger.LogInformation("User {UserId} logged in successfully", userId);

// BAD - Logs full request body
_logger.LogInformation("Request body: {Body}", requestBody);

// GOOD - Logs sanitized info
_logger.LogInformation("Processing {RequestType} request for user {UserId}", 
    requestType, userId);
```

## PII Masking

### When PII Must Be Logged
If PII must be logged for debugging, mask it:

```csharp
public static string MaskEmail(string email)
{
    if (string.IsNullOrEmpty(email)) return email;
    
    var parts = email.Split('@');
    if (parts.Length != 2) return "***";
    
    var local = parts[0];
    var masked = local.Length > 2 
        ? $"{local.Substring(0, 2)}***" 
        : "***";
    
    return $"{masked}@{parts[1]}";
}

_logger.LogInformation(
    "Password reset requested for {Email}",
    MaskEmail(email)
);
```

## Performance Logging

### Response Time Tracking
```csharp
var stopwatch = Stopwatch.StartNew();
try
{
    // Operation
    var result = await _service.ProcessAsync();
    
    _logger.LogInformation(
        "Operation {OperationName} completed in {Duration}ms",
        nameof(ProcessAsync),
        stopwatch.ElapsedMilliseconds
    );
    
    return result;
}
catch (Exception ex)
{
    _logger.LogError(
        ex,
        "Operation {OperationName} failed after {Duration}ms",
        nameof(ProcessAsync),
        stopwatch.ElapsedMilliseconds
    );
    throw;
}
```

### Slow Query Logging
```csharp
if (stopwatch.ElapsedMilliseconds > 500)
{
    _logger.LogWarning(
        "Slow query detected: {Query} took {Duration}ms",
        queryName,
        stopwatch.ElapsedMilliseconds
    );
}
```

## Error Logging

### Exception Logging
```csharp
catch (Exception ex)
{
    _logger.LogError(
        ex,
        "Error processing request for user {UserId}. CorrelationId: {CorrelationId}",
        userId,
        correlationId
    );
    
    // Include relevant context
    _logger.LogError(
        "Request details - Method: {Method}, Path: {Path}, Parameters: {Parameters}",
        httpContext.Request.Method,
        httpContext.Request.Path,
        SanitizeParameters(parameters)
    );
}
```

### Global Exception Handler
```csharp
public class ExceptionLoggingMiddleware
{
    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            _logger.LogError(
                ex,
                "Unhandled exception in request {Method} {Path}",
                context.Request.Method,
                context.Request.Path
            );
            
            // Return user-friendly error
            context.Response.StatusCode = 500;
            await context.Response.WriteAsJsonAsync(new
            {
                error = "An error occurred processing your request",
                correlationId = context.TraceIdentifier
            });
        }
    }
}
```

## Log Storage and Retention

### Storage Strategy
- **Development**: Console + File
- **UAT**: File + Centralized logging (ELK, Application Insights)
- **Production**: Centralized logging only

### Retention Policy
- **Trace/Debug**: Not stored in production
- **Information**: 30 days
- **Warning**: 90 days
- **Error/Critical**: 1 year
- **Audit logs**: Per compliance requirements (typically 7 years)

### Log Rotation
- Rotate daily or when file reaches 100MB
- Compress old log files
- Archive to cold storage after retention period

## Centralized Logging

### Log Aggregation
- Use ELK Stack (Elasticsearch, Logstash, Kibana)
- Or Azure Application Insights
- Or Splunk, Datadog, etc.

### Benefits
- Search across all services
- Correlate logs from different sources
- Create dashboards and alerts
- Analyze trends and patterns

## Monitoring and Alerting

### Alert on Critical Events
- Application crashes
- High error rates (> 5%)
- Authentication failures spike
- Database connection failures
- Slow response times

### Alert Configuration Example
```
Alert: High Error Rate
Condition: Error count > 100 in 5 minutes
Severity: Critical
Notification: Email + SMS to on-call team
```

## Log Analysis

### Regular Reviews
- Daily review of errors and warnings
- Weekly trend analysis
- Monthly performance review
- Quarterly security audit

### Key Metrics to Track
- Error rate by endpoint
- Average response time
- Failed authentication attempts
- Slow queries
- Exception frequency by type

## Frontend Logging

### Browser Console Logging
```typescript
// Development only
if (process.env.NODE_ENV === 'development') {
  console.log('Debug info:', data);
}

// Production - use proper logger
logger.info('User action', { action: 'click', element: 'submit-button' });
```

### Error Tracking
```typescript
// Integrate with Sentry or similar
try {
  // Operation
} catch (error) {
  logger.error('Operation failed', {
    error: error.message,
    stack: error.stack,
    userId: user.id,
    correlationId: getCorrelationId()
  });
  
  // Send to error tracking service
  Sentry.captureException(error);
}
```

## Compliance and Audit Logging

### Audit Trail Requirements
- Log all access to sensitive data
- Log all data modifications
- Log administrative actions
- Log permission changes
- Include user ID and timestamp

### Audit Log Example
```csharp
_auditLogger.LogInformation(
    "User {UserId} accessed PII for customer {CustomerId} at {Timestamp}. Reason: {Reason}",
    userId,
    customerId,
    DateTime.UtcNow,
    accessReason
);
```

### Audit Log Retention
- Store separately from application logs
- Immutable (cannot be modified or deleted)
- Retain per compliance requirements
- Regular audit reviews

## Best Practices

1. **Use appropriate log levels** - Don't log everything as Information
2. **Include context** - Add relevant details to help debugging
3. **Be consistent** - Use standard message formats
4. **Think about the reader** - Write logs that will help future you
5. **Test your logging** - Verify logs are helpful during debugging
6. **Monitor log volume** - Excessive logging can impact performance
7. **Sanitize inputs** - Never trust user input in logs
8. **Use correlation IDs** - Track requests across services
9. **Log at boundaries** - Entry/exit points of major operations
10. **Review regularly** - Logs are only useful if someone reads them
