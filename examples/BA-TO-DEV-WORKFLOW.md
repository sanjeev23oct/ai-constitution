# BA to Dev Workflow: From Prototype to Production

## Overview

This workflow enables Business Analysts to create functional prototypes using vibe coding, then automatically generate formal requirements, design, and tasks for the development team to build production-ready applications.

## The Complete Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    PHASE 1: BA PROTOTYPING                      │
│                      (Vibe Coding)                              │
└─────────────────────────────────────────────────────────────────┘
                              ↓
                    BA creates prototype
                    (Single HTML file)
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│              PHASE 2: REQUIREMENTS EXTRACTION                   │
│                  (BA with AI Assistance)                        │
└─────────────────────────────────────────────────────────────────┘
                              ↓
              Generate requirements.md from prototype
              (EARS format, user stories, acceptance criteria)
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                   PHASE 3: JIRA HANDOFF                         │
│                  (BA → Dev Transition)                          │
└─────────────────────────────────────────────────────────────────┘
                              ↓
              BA creates Jira ticket with:
              - Prototype (HTML file attached)
              - Requirements.md (attached or linked)
              - Stakeholder approval notes
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│              PHASE 4: DEV SPEC CREATION                         │
│                  (Kiro Spec Flow)                               │
└─────────────────────────────────────────────────────────────────┘
                              ↓
              Dev pulls Jira ticket
              Dev runs Kiro spec flow:
              1. Import requirements.md
              2. Kiro generates design.md
              3. Kiro generates tasks.md
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│              PHASE 5: PRODUCTION DEVELOPMENT                    │
│                  (Dev Team Implementation)                      │
└─────────────────────────────────────────────────────────────────┘
                              ↓
              Dev team executes tasks in Kiro
              (Production code following standards)
```

---

## Phase 1: BA Creates Prototype (Vibe Coding)

### What BA Does:

**Step 1:** Open Kiro and describe what you want

```
Prompt: "Create a CRM dashboard prototype with:
- Key metrics cards (Revenue, Customers, Deals, Avg Deal Size)
- Revenue trend chart for last 6 months
- Customer list table with search and filter
- Add customer functionality
- Contact management
- Export to CSV and Excel
- Professional design suitable for executives"
```

**Step 2:** Kiro generates `crm-dashboard-prototype.html`

**Step 3:** Open in browser, test with stakeholders, iterate as needed

```
Prompt: "Add a date range filter to the customer list"
Prompt: "Change the primary color to match our brand (#2563eb)"
Prompt: "Add a notes field to the customer form"
```

**Step 4:** Get stakeholder approval on the prototype

### Output:
- ✅ `crm-dashboard-prototype.html` - Approved functional prototype
- ✅ Stakeholder sign-off
- ✅ Clear understanding of what needs to be built

---

## Phase 2: Requirements Extraction

### What BA Does:

**Step 1:** Ask Kiro to analyze the prototype and generate requirements

```
Prompt: "Analyze examples/crm-dashboard-prototype.html and generate 
a formal requirements.md file following EARS format. Include:
- Introduction and glossary
- User stories for each major feature
- Acceptance criteria using EARS patterns
- Business rules (like customer tier calculation)
- Non-functional requirements
Save it to .kiro/specs/crm-dashboard/requirements.md"
```

**Step 2:** Review the generated requirements

**Step 3:** Refine if needed

```
Prompt: "Add a requirement for email notifications when a 
customer becomes at-risk"
```

**Step 4:** Get stakeholder approval on requirements

### Output:
- ✅ `.kiro/specs/crm-dashboard/requirements.md` - Formal EARS requirements
- ✅ All features documented with acceptance criteria
- ✅ Business rules clearly stated
- ✅ Ready for design phase

### Example Requirements Generated:

See `examples/crm-requirements.md` for a complete example extracted from our prototype!

Key sections include:
- **Requirement 1:** Dashboard Metrics Display
- **Requirement 4:** Customer Tier Classification (with business rules)
- **Requirement 5:** Customer Search and Filtering
- **Requirement 7:** Customer Data Export
- **Requirement 8:** Contact Management

---

## Phase 3: Jira Handoff

### What BA Does:

**Step 1:** Create Jira ticket with all artifacts

**Jira Ticket Structure:**
```
Title: [Feature] CRM Dashboard

Description:
- Link to prototype: [Attach crm-dashboard-prototype.html]
- Link to requirements: [Attach requirements.md]
- Stakeholder: [Name] - Approved on [Date]
- Priority: High
- Sprint: [Sprint Number]

Acceptance Criteria:
[Copy from requirements.md or reference it]

Attachments:
- crm-dashboard-prototype.html
- requirements.md
- screenshots/demo-video (optional)
```

**Step 2:** Assign to dev team or add to backlog

**Step 3:** BA is done! Handoff complete.

### Output:
- ✅ Jira ticket created with all context
- ✅ Prototype attached for reference
- ✅ Requirements documented
- ✅ Ready for dev team to pick up

---

## Phase 4: Dev Spec Creation (Kiro Spec Flow)

### What Developer Does:

**Step 1:** Pull Jira ticket and download attachments

**Step 2:** Create spec in Kiro

```
In Kiro:
1. Create new spec: "crm-dashboard"
2. Copy requirements.md to .kiro/specs/crm-dashboard/requirements.md
3. Open prototype.html in browser for reference
```

**Step 3:** Run Kiro Spec Flow - Generate Design

```
Prompt: "Review the requirements in .kiro/specs/crm-dashboard/requirements.md 
and create a design.md file following our production standards.

Include:
- System architecture (React frontend, .NET 9.0 API, PostgreSQL)
- Component breakdown
- API endpoints with request/response contracts
- Database schema with stored procedures
- Authentication flow with Authentik
- Data flow diagrams
- Error handling strategy

Reference the prototype at examples/crm-dashboard-prototype.html 
for UI/UX guidance."
```

Kiro generates: `.kiro/specs/crm-dashboard/design.md`

**Step 4:** Review design, iterate if needed

```
Prompt: "Add caching strategy using Redis for customer list"
Prompt: "Include rate limiting for API endpoints"
```

**Step 5:** Get design approved (tech lead review)

**Step 6:** Run Kiro Spec Flow - Generate Tasks

```
Prompt: "Based on the design.md, create a tasks.md file with 
step-by-step implementation tasks following our standards.

Break down into:
- Database setup (tables, stored procedures, Liquibase migrations)
- API development (.NET controllers, services, repositories)
- Frontend development (React components, hooks, services)
- Integration and testing
- Mark unit tests as optional with *

Each task should reference specific requirements."
```

Kiro generates: `.kiro/specs/crm-dashboard/tasks.md`

**Step 7:** Review tasks, prioritize, and start implementation

### Output:
- ✅ `.kiro/specs/crm-dashboard/design.md` - Technical design
- ✅ `.kiro/specs/crm-dashboard/tasks.md` - Implementation checklist
- ✅ Architecture decisions documented
- ✅ Ready for development

### Example Task Structure:

```markdown
# Implementation Plan

- [ ] 1. Database Setup
  - [ ] 1.1 Create customers table with tier calculation
  - [ ] 1.2 Create contacts table with foreign key to customers
  - [ ] 1.3 Create Liquibase migration scripts
  - _Requirements: 3, 4, 8_

- [ ] 2. API Development
  - [ ] 2.1 Implement GET /api/v1/customers endpoint
  - [ ] 2.2 Implement POST /api/v1/customers endpoint
  - [ ] 2.3 Implement customer tier calculation stored procedure
  - _Requirements: 3, 4, 6_

- [ ] 3. Frontend Components
  - [ ] 3.1 Create MetricsCard component
  - [ ] 3.2 Create CustomerTable component with search/filter
  - [ ] 3.3 Create AddCustomerModal component
  - _Requirements: 1, 3, 5, 6_
```

---

## Phase 5: Production Development

### What Dev Team Does:

**Step 1:** Open `.kiro/specs/crm-dashboard/tasks.md` in Kiro

**Step 2:** Click "Start Task" on first task

**Step 3:** Kiro generates production code following your constitution:
- .NET 9.0 API with proper structure
- React components with TypeScript
- PostgreSQL stored procedures
- Liquibase migrations
- Unit tests
- All following your standards!

**Step 4:** Review, test, commit

**Step 5:** Move to next task

### Output:
- ✅ Production-ready code
- ✅ Follows all company standards
- ✅ Properly tested
- ✅ Ready for deployment

---

## Key Benefits of This Workflow

### For Business Analysts:
✅ Create prototypes quickly (minutes, not days)
✅ Get stakeholder approval on working demos
✅ No coding skills required
✅ Automatic generation of formal requirements
✅ Clear handoff to dev team

### For Development Team:
✅ Clear requirements with acceptance criteria
✅ Technical design already done
✅ Step-by-step implementation tasks
✅ Code generation follows company standards
✅ Reduced back-and-forth with BAs

### For the Organization:
✅ Faster time to market
✅ Fewer misunderstandings
✅ Better quality (standards enforced)
✅ Clear audit trail (prototype → requirements → design → tasks → code)
✅ Reusable patterns and components

---

## Example Prompts for Each Phase

### Phase 1: Prototype Creation
```
"Create a [feature] prototype with [list of features]"
"Add [new feature] to the prototype"
"Change [element] to [new design]"
"Make it look more professional for executive presentation"
```

### Phase 2: Requirements Extraction (BA)
```
"Analyze [prototype.html] and generate requirements.md with EARS format"
"Add requirement for [new feature] to requirements.md"
"Refine requirement [number] to include [additional criteria]"
```

### Phase 3: Jira Handoff (BA)
```
- Create Jira ticket
- Attach prototype.html
- Attach requirements.md
- Add stakeholder approval notes
- Assign to dev team
```

### Phase 4: Design Generation (Developer)
```
"Review requirements.md and create design.md using our production tech stack"
"Add [architectural component] to the design"
"Include API contracts for [feature]"
"Add authentication flow with Authentik"
```

### Phase 5: Task Generation (Developer)
```
"Generate tasks.md from design.md with implementation steps"
"Break down task [number] into smaller subtasks"
"Add testing tasks for [feature]"
```

### Phase 5: Implementation
```
"Implement task 1.1: Create customers table"
"Fix the failing test in CustomerService"
"Add validation to the customer creation endpoint"
```

---

## Tips for Success

### For BAs:
1. **Start simple** - Create basic prototype first, add features iteratively
2. **Get feedback early** - Show prototype to stakeholders before generating requirements
3. **Document business rules** - Make sure tier calculations, status rules, etc. are clear
4. **Use realistic data** - Mock data should look like real customer data

### For Developers:
1. **Review Jira ticket** - Understand the context and stakeholder needs
2. **Open prototype** - Keep it open while creating design and tasks
3. **Generate design** - Use Kiro spec flow with production standards
4. **Generate tasks** - Break down into manageable implementation steps
5. **Get approval** - Have tech lead review design before starting implementation

### For Developers:
1. **Reference prototype** - Keep it open while implementing
2. **Follow tasks in order** - They're designed to build incrementally
3. **Ask questions early** - If requirements are unclear, ask BA before coding
4. **Test against acceptance criteria** - Each requirement has clear criteria

---

## Common Questions

**Q: Can we skip the prototype and go straight to requirements?**
A: You can, but the prototype helps stakeholders visualize and approve the concept before investing in development.

**Q: What if stakeholders want changes after requirements are generated?**
A: Update the prototype, regenerate requirements, and update design/tasks accordingly.

**Q: Can developers modify the generated code?**
A: Absolutely! Generated code is a starting point. Developers refine and optimize as needed.

**Q: How do we handle changes during development?**
A: Update the relevant document (requirements, design, or tasks) and regenerate downstream artifacts.

**Q: What if the prototype has features we don't want in production?**
A: When generating requirements, specify which features to include/exclude.

---

## Success Metrics

Track these to measure workflow effectiveness:

- **Time from idea to approved prototype**: Target < 1 day
- **Time from prototype to requirements**: Target < 2 hours
- **Requirements clarity**: Measure by number of clarification questions from dev team
- **Development velocity**: Compare before/after adopting this workflow
- **Stakeholder satisfaction**: Measure approval rate on first prototype iteration

---

## Next Steps

1. **Train BAs** on prototype creation with Kiro + Constitution
2. **Create example prompts** library for common features
3. **Establish review process** for each phase
4. **Set up templates** for requirements, design, and tasks
5. **Measure and iterate** on the workflow

---

## Conclusion

This workflow bridges the gap between business vision and technical implementation. BAs create what stakeholders want to see (prototypes), and developers get what they need to build (requirements, design, tasks). Everyone wins!

The key is the constitution files that ensure consistency at every phase - from prototype styling to production code standards.
