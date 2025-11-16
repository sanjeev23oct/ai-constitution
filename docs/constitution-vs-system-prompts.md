# Constitution Files vs System Prompts: Understanding the Difference

## Overview

When working with AI coding assistants, there are two types of instructions that guide the AI's behavior: **System Prompts** (controlled by the vendor) and **Constitution Files** (controlled by you). Understanding the difference is crucial for maximizing the value of AI-assisted development.

## What Are System Prompts?

**System Prompts** are hidden instructions provided by the AI tool vendor (OpenAI, Anthropic, Kiro, etc.) that define the AI's base behavior.

### Characteristics:
- **Hidden**: Not visible to end users
- **Vendor-Controlled**: Only the tool creator can modify them
- **Generic**: Same for all users of the tool
- **Foundation Layer**: Provides basic guidelines and safety rails

### Example System Prompt:
```
You are Kiro, an AI coding assistant.
You should write clean, maintainable code.
Follow software engineering best practices.
Be helpful, accurate, and professional.
Do not provide harmful or malicious code.
```

### Purpose:
- Establish the AI's identity and role
- Set general behavioral guidelines
- Implement safety and ethical constraints
- Ensure consistent baseline behavior

## What Are Constitution Files?

**Constitution Files** (also called Steering Documents) are custom markdown files that you create and control, containing your team's specific standards, practices, and business rules.

### Characteristics:
- **Visible**: Plain markdown files in your repository
- **User-Controlled**: You create, edit, and version them
- **Specific**: Tailored to your tech stack and practices
- **Contextual**: Automatically loaded by the AI tool

### Example Constitution File:
```markdown
# API Development Standards

## Technology Stack
- Framework: .NET 9.0
- Database: PostgreSQL with stored procedures
- Authentication: Authentik (OAuth 2.0)

## Business Rules
- Customer Tier Calculation:
  - Bronze: < $10,000 annual revenue
  - Silver: $10,000 - $50,000
  - Gold: $50,000 - $100,000
  - Platinum: > $100,000

## Code Standards
- Use stored procedures for all business logic
- Never log PII data
- Return standardized JSON response format
```

### Purpose:
- Encode your team's specific standards
- Document business rules and logic
- Ensure consistency across all AI-generated code
- Provide context about your architecture and decisions

## Key Differences

| Aspect | System Prompts | Constitution Files |
|--------|---------------|-------------------|
| **Visibility** | Hidden from users | Visible markdown files |
| **Control** | Vendor controls | You control |
| **Customization** | Not customizable | Fully customizable |
| **Scope** | All users (generic) | Your team (specific) |
| **Version Control** | No | Yes (Git) |
| **Sharing** | Cannot share | Can share via GitHub |
| **Updates** | Vendor decides when | You decide when |
| **Specificity** | Generic best practices | Your tech stack & rules |
| **Business Context** | No business rules | Your business rules |
| **Examples** | No code examples | Your code patterns |
| **Evolution** | Slow (vendor updates) | Fast (you update anytime) |

## How They Work Together

Constitution files and system prompts work in layers:

```
┌─────────────────────────────────────────────────┐
│  Layer 1: System Prompt (Foundation)            │
│  "Be a helpful coding assistant"                │
│  "Follow best practices"                        │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  Layer 2: Constitution Files (Your Standards)   │
│  "Use .NET 9.0 with PostgreSQL"                 │
│  "Customer tiers: Bronze, Silver, Gold..."      │
│  "Authenticate with Authentik"                  │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  Layer 3: Your Prompt (Specific Task)           │
│  "Create a customer registration API"          │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  Result: Code following best practices (L1)     │
│  using your tech stack (L2) for your need (L3)  │
└─────────────────────────────────────────────────┘
```

## Real-World Analogy

**System Prompts = Company Policy Manual**
- Same for everyone in the company
- You cannot change it
- Generic rules: "Be professional", "Follow the law"
- HR/Management controls it

**Constitution Files = Your Team's Playbook**
- Specific to your team
- You write and update it
- Specific practices: "We use .NET", "We deploy on Fridays"
- Your team controls it

**Your Prompt = Today's Task**
- What you want to do right now
- Changes every time
- Specific request: "Build customer API"

## Impact on Development Workflow

### Without Constitution Files

```
Developer: "Create a customer API"
AI: "Here's a Node.js API with JWT auth and MongoDB..."
Developer: "No, we use .NET and PostgreSQL"
AI: "Here's .NET with Entity Framework..."
Developer: "No, we use stored procedures"
AI: "Here's .NET with stored procedures..."
Developer: "Add customer tier calculation"
AI: "What are the tier rules?"
Developer: [Explains rules again]
```

**Result:** 5+ back-and-forth messages, frustration, inconsistency

### With Constitution Files

```
Developer: "Create a customer API"
AI: "Here's .NET 9.0 API with:
     - Stored procedure sp_create_customer
     - Authentik OAuth integration
     - Customer tier calculation (Bronze/Silver/Gold/Platinum)
     - Standardized response format
     - No PII in logs
     - Following your API standards"
```

**Result:** 1 message, perfect result, consistent with team standards

## Why Constitution Files Are More Powerful

### 1. Specificity

**System Prompt (Generic):**
```
"Use best practices for API development"
```

**Constitution File (Specific):**
```markdown
## API Response Format
All API responses must follow this structure:
{
  "success": boolean,
  "data": object | null,
  "message": string,
  "errors": array | null
}
```

### 2. Business Context

**System Prompt:**
```
"Follow business rules"
```

**Constitution File:**
```markdown
## Customer Tier Calculation
- Bronze: < $10,000 annual revenue
- Silver: $10,000 - $50,000
- Gold: $50,000 - $100,000
- Platinum: > $100,000

Discussed with: John Smith (Sales Director)
Date: 2024-11-15
Rationale: Aligns with sales commission structure
```

### 3. Code Examples

**System Prompt:**
```
"Write clean code"
```

**Constitution File:**
```markdown
## Stored Procedure Pattern
```sql
CREATE OR REPLACE PROCEDURE sp_create_user(
    p_email VARCHAR(255),
    OUT p_user_id INTEGER,
    OUT p_status_code INTEGER,
    OUT p_message TEXT
)
LANGUAGE plpgsql AS $$
BEGIN
    -- Validation
    IF p_email IS NULL THEN
        p_status_code := 400;
        p_message := 'Email is required';
        RETURN;
    END IF;
    
    -- Business logic here
END;
$$;
```
```

### 4. Architectural Decisions

**System Prompt:**
```
"Use appropriate architecture"
```

**Constitution File:**
```markdown
## Architecture Decision: Why Stored Procedures?

**Decision:** Use PostgreSQL stored procedures for all business logic

**Rationale:**
- Centralized business logic close to data
- Better performance (reduced network round-trips)
- Easier to maintain consistency across applications
- Database-level validation and constraints

**Alternatives Considered:**
- ORM-only: Rejected due to performance concerns
- Hybrid: Rejected due to added complexity
```

## Benefits of Constitution Files

### For Development Teams:
✅ **Consistency**: All code follows the same standards
✅ **Speed**: No need to repeat standards in every prompt
✅ **Quality**: Standards are enforced automatically
✅ **Onboarding**: New developers learn standards through generated code
✅ **Documentation**: Standards are living documents, always up-to-date

### For Organizations:
✅ **Scalability**: Standards scale across teams and projects
✅ **Compliance**: Security and regulatory requirements encoded
✅ **Knowledge Preservation**: Tribal knowledge documented
✅ **Audit Trail**: Version-controlled standards with history
✅ **Flexibility**: Easy to update as practices evolve

## Creating Effective Constitution Files

### Best Practices:

1. **Separate by Domain**
   - `api-standards.md` - Backend API development
   - `ui-standards.md` - Frontend development
   - `database-standards.md` - Database design and access
   - `security-standards.md` - Security requirements
   - `deployment-standards.md` - CI/CD and deployment

2. **Include Examples**
   - Show code patterns, not just descriptions
   - Provide good and bad examples
   - Include real code from your codebase

3. **Document the "Why"**
   - Explain architectural decisions
   - Include context and rationale
   - Reference discussions and decision-makers

4. **Keep It Practical**
   - Focus on what matters during development
   - Avoid theoretical fluff
   - Make it actionable

5. **Evolve Over Time**
   - Update as you learn
   - Remove what doesn't work
   - Add new patterns as they emerge

## Common Misconceptions

### Misconception 1: "System prompts are enough"
**Reality:** System prompts are generic. They don't know your tech stack, business rules, or team practices.

### Misconception 2: "Constitution files are just documentation"
**Reality:** They're executable knowledge. The AI uses them to generate code that follows your standards.

### Misconception 3: "I can just tell the AI my standards each time"
**Reality:** That's inefficient and leads to inconsistency. Constitution files ensure standards are always applied.

### Misconception 4: "Constitution files lock me into specific tools"
**Reality:** They're just markdown files. They work with any AI tool that supports custom context.

## Conclusion

**System prompts** provide the foundation—they make the AI a helpful coding assistant.

**Constitution files** provide the specialization—they make the AI YOUR team's coding assistant that knows YOUR standards, YOUR tech stack, and YOUR business rules.

Together, they transform a generic AI tool into a powerful, team-specific development accelerator that ensures consistency, quality, and efficiency across all your projects.

The key insight: **Generic AI + Your Constitution = Your Custom AI Development Team**

---

## Further Reading

- [Building a Self-Documenting AI Development Workflow](../medium-article.md)
- [BA to Dev Workflow](../examples/BA-TO-DEV-WORKFLOW.md)
- [Prototype Standards](../.kiro/steering/prototype-standards.md)
- [API Standards](../.kiro/steering/api-standards.md)
