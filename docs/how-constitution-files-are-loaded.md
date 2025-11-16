# How Constitution Files Are Loaded in Kiro

## Overview

Understanding how Kiro loads and applies constitution files (steering documents) is crucial for organizing your team's standards effectively. This document explains the loading mechanism, best practices, and advanced configuration options.

## Default Loading Behavior

### All Files Loaded by Default

By default, Kiro loads **ALL** markdown files in the `.kiro/steering/` directory as context for every interaction.

```
.kiro/steering/
├── api-standards.md          ← Always loaded
├── ui-standards.md           ← Always loaded
├── database-standards.md     ← Always loaded
├── security-standards.md     ← Always loaded
├── deployment-standards.md   ← Always loaded
├── quality-standards.md      ← Always loaded
├── logging-standards.md      ← Always loaded
├── architecture-decisions.md ← Always loaded
└── prototype-standards.md    ← Always loaded
```

### Why This Is Good

**Cross-Cutting Concerns Always Apply:**
- Security standards (PII handling, authentication) apply to all code
- Quality standards (testing requirements) apply to all features
- Logging standards (what to log, what not to log) apply everywhere
- Architecture decisions provide context for all development

**No Configuration Needed:**
- Simple to set up
- No risk of forgetting important standards
- Works out of the box

**Intelligent Application:**
- Kiro's AI determines which standards are most relevant to your prompt
- Relevant standards are prioritized in the response
- All standards are available for reference

## How Kiro Applies Constitution Files

### The Process

1. **Load All Files**: All `.md` files in `.kiro/steering/` are loaded into context
2. **Analyze Prompt**: Kiro analyzes your request to understand what you're trying to do
3. **Prioritize Relevant Standards**: Standards most relevant to your task are given higher weight
4. **Apply Cross-Cutting Concerns**: Security, quality, and logging standards always apply
5. **Generate Response**: Code/content follows all applicable standards

### Example Scenarios

#### Scenario 1: Creating a Prototype

**Your Prompt:**
```
"Create a CRM dashboard prototype with customer list and metrics"
```

**What Kiro Loads:**
- ✅ All constitution files (default behavior)

**What Kiro Prioritizes:**
- 🎯 `prototype-standards.md` - Primary focus (HTML/JS, Tailwind, single file)
- 🎯 `security-standards.md` - PII handling, input validation
- 🎯 `quality-standards.md` - Testing approach (if applicable)

**Result:**
- HTML prototype following prototype standards
- No PII in mock data
- Professional, modern design
- Single file, no build tools

#### Scenario 2: Creating Production API

**Your Prompt:**
```
"Create a customer registration API endpoint"
```

**What Kiro Loads:**
- ✅ All constitution files (default behavior)

**What Kiro Prioritizes:**
- 🎯 `api-standards.md` - Primary focus (.NET 9.0, RESTful, validation)
- 🎯 `database-standards.md` - Stored procedures, data access
- 🎯 `security-standards.md` - Authentication, PII encryption
- 🎯 `logging-standards.md` - What to log, structured logging
- 🎯 `architecture-decisions.md` - Why stored procedures, tech stack

**Result:**
- .NET 9.0 API controller
- Stored procedure for business logic
- Authentik authentication
- Proper error handling
- Structured logging (no PII)
- Following all production standards

#### Scenario 3: Creating React Component

**Your Prompt:**
```
"Create a CustomerList component with search and filter"
```

**What Kiro Loads:**
- ✅ All constitution files (default behavior)

**What Kiro Prioritizes:**
- 🎯 `ui-standards.md` - Primary focus (React, TypeScript, component patterns)
- 🎯 `security-standards.md` - PII masking in UI, XSS prevention
- 🎯 `quality-standards.md` - Testing requirements, accessibility
- 🎯 `api-standards.md` - API integration patterns

**Result:**
- React component with TypeScript
- Proper hooks usage
- PII masking for sensitive data
- Accessible markup
- API integration following standards

## Advanced Configuration: Conditional Loading

While the default behavior (load all files) works well for most teams, Kiro supports conditional loading for special cases.

### Option 1: File Match Pattern

Load a constitution file only when working with specific file types.

**Use Case:** You have platform-specific standards (iOS, Android, Web) and want to load only relevant ones.

**Configuration:**
```markdown
---
inclusion: fileMatch
fileMatchPattern: '*.tsx'
---

# React Component Standards

This file will only be loaded when working with .tsx files
```

**Example Patterns:**
- `*.tsx` - TypeScript React files
- `*.cs` - C# files
- `*.sql` - SQL files
- `**/*.test.ts` - Test files
- `src/mobile/**/*` - Mobile app files

### Option 2: Manual Inclusion

Load a constitution file only when explicitly referenced.

**Use Case:** You have rarely-used standards (legacy system integration, special compliance rules) that add noise if always loaded.

**Configuration:**
```markdown
---
inclusion: manual
---

# Legacy System Integration Standards

This file will only be loaded when you use #legacy-system-integration in chat
```

**How to Use:**
```
You: "Integrate with legacy system #legacy-system-integration"
Kiro: [Loads legacy-system-integration.md and applies those standards]
```

### Option 3: Always Included (Default)

No front-matter needed. This is the default behavior.

```markdown
# API Development Standards

This file is always loaded (default behavior)
```

## Best Practices

### 1. Keep Default Behavior for Most Files

**Recommended Approach:**
- Let Kiro load all files by default
- Trust Kiro's intelligence to prioritize relevant standards
- Only use conditional loading for special cases

**Why:**
- Simpler to manage
- No configuration overhead
- Cross-cutting concerns always enforced
- No risk of forgetting important standards

### 2. Organize by Domain

**Good Structure:**
```
.kiro/steering/
├── api-standards.md          # Backend API development
├── ui-standards.md           # Frontend development
├── database-standards.md     # Database design and access
├── security-standards.md     # Security (always relevant)
├── deployment-standards.md   # CI/CD and deployment
├── quality-standards.md      # Testing and QA (always relevant)
├── logging-standards.md      # Logging (always relevant)
└── architecture-decisions.md # ADRs (context for all)
```

**Why This Works:**
- Clear separation of concerns
- Easy to find and update standards
- Kiro can intelligently prioritize based on domain
- Cross-cutting concerns (security, quality, logging) always apply

### 3. Use Descriptive Filenames

Kiro can infer relevance from filenames:

**Good Filenames:**
- ✅ `api-standards.md` - Clear it's for APIs
- ✅ `react-component-standards.md` - Clear it's for React
- ✅ `prototype-standards.md` - Clear it's for prototypes
- ✅ `mobile-app-standards.md` - Clear it's for mobile

**Poor Filenames:**
- ❌ `standards.md` - Too vague
- ❌ `rules.md` - Too generic
- ❌ `team-guidelines.md` - Unclear scope

### 4. Keep Files Focused

Each file should cover one domain:

**Good:**
```markdown
# API Development Standards

## Technology Stack
- Framework: .NET 9.0
- Authentication: Authentik

## API Design
[API-specific content only]
```

**Bad:**
```markdown
# Everything Standards

## APIs
[API content]

## UI
[UI content]

## Database
[Database content]

[Too broad, hard to maintain]
```

### 5. Document Cross-Cutting Concerns Separately

Some standards apply to everything:

**Always-Relevant Files:**
- `security-standards.md` - Authentication, PII, encryption
- `quality-standards.md` - Testing, code coverage
- `logging-standards.md` - What to log, what not to log

**Why Separate:**
- These apply to all code (API, UI, database)
- Easier to maintain in one place
- Clear that they're always enforced

## Token Management

### Question: Won't Loading All Files Use Too Many Tokens?

**Answer:** Kiro is designed to handle multiple constitution files efficiently:

**Token Budget:**
- You have a 200K token budget per interaction
- Constitution files typically use 10-30K tokens total
- Plenty of room for your prompts and responses

**Intelligent Loading:**
- Kiro loads files efficiently
- Only relevant sections are heavily weighted
- The system is optimized for this use case

**Best Practice:**
- Don't worry about token usage for constitution files
- Focus on keeping files focused and well-organized
- Let Kiro handle the optimization

## Checking What's Loaded

You can see which constitution files are currently loaded by looking at the system context in your conversation.

**Example:**
```
## Included Rules (api-standards.md) [Workspace]
## Included Rules (ui-standards.md) [Workspace]
## Included Rules (security-standards.md) [Workspace]
...
```

This shows which files are active in the current conversation.

## Common Questions

### Q: Should I use conditional loading?

**A:** Only for special cases:
- ✅ Use default (always load) for 90% of files
- ✅ Use fileMatch for platform-specific standards (iOS vs Android)
- ✅ Use manual for rarely-used, specialized standards
- ❌ Don't use conditional loading for core standards

### Q: How does Kiro know which standards to apply?

**A:** Kiro's AI analyzes:
- Your prompt content
- File names and structure
- Context from previous messages
- Relevance of each standard to your task

### Q: Can I have too many constitution files?

**A:** Practically, no:
- 5-15 files is typical and works great
- 20-30 files is fine if well-organized
- Focus on organization, not quantity
- Keep files focused and domain-specific

### Q: What if standards conflict?

**A:** Organize to avoid conflicts:
- Keep files domain-specific (API vs UI vs Database)
- Document exceptions in the relevant file
- Use architecture-decisions.md to explain trade-offs
- If conflicts arise, consolidate or clarify standards

### Q: Can I override a standard for a specific task?

**A:** Yes, in your prompt:
```
"Create a customer API, but use Entity Framework instead of 
stored procedures for this prototype"
```

Kiro will follow your explicit instruction while still applying other standards.

## Migration Guide

### From Single File to Multiple Files

If you started with one large constitution file:

**Step 1: Identify Domains**
- API development
- UI development
- Database
- Security
- Quality/Testing
- Deployment

**Step 2: Split Content**
- Create separate files for each domain
- Move relevant content to each file
- Keep cross-cutting concerns separate

**Step 3: Test**
- Try prompts for different domains
- Verify Kiro applies correct standards
- Refine organization as needed

**Step 4: Remove Old File**
- Once new files work well, delete the old monolithic file

## Conclusion

**Key Takeaways:**

1. **Default Behavior:** Kiro loads all constitution files by default
2. **Intelligent Application:** Kiro prioritizes relevant standards for each task
3. **Cross-Cutting Concerns:** Security, quality, and logging always apply
4. **Keep It Simple:** Use default loading for most files
5. **Organize by Domain:** Clear separation makes maintenance easier
6. **Trust the System:** Kiro is designed to handle multiple files efficiently

**The Bottom Line:**

Don't overthink constitution file loading. The default behavior (load all files) works great for most teams. Focus on:
- Writing clear, focused standards
- Organizing by domain
- Including good examples
- Keeping files up to date

Let Kiro handle the intelligent application of your standards!

## Further Reading

- [Constitution Files vs System Prompts](./constitution-vs-system-prompts.md)
- [BA to Dev Workflow](../examples/BA-TO-DEV-WORKFLOW.md)
- [Kiro Steering Documentation](https://docs.kiro.ai/steering) (if available)
