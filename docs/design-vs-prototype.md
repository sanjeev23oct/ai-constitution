# Design vs Prototype: Understanding the Difference

## The Simple Answer

**Design** = "Here's what it will **LOOK** like"  
**Prototype** = "Here's how it will **WORK**"

## The Car Analogy

Think of it like buying a car:

**Design** = Picture of a car
- Shows what it looks like
- Colors, shape, style
- Beautiful photography
- **You CANNOT drive it**

**Prototype** = Test drive
- Actually works
- Can drive it around
- Feel how it handles
- Test the features
- **You CAN experience it**

**Production** = The actual car you buy
- Real engine
- Real materials
- Fully functional
- Ready for daily use
- **You OWN and use it daily**

---

## Detailed Comparison

| Aspect | Design | Prototype | Production |
|--------|--------|-----------|------------|
| **Purpose** | Visual appearance | Functionality demonstration | Real product |
| **Shows** | Layout, colors, typography | Interactions, workflows, logic | Everything |
| **Interactivity** | Static or basic clicks | Fully functional | Fully functional + backend |
| **Data** | Placeholder text | Mock data that behaves realistically | Real data from database |
| **Business Logic** | None | Demonstrates actual rules | Enforces actual rules |
| **Tools** | Figma, Sketch, Adobe XD | HTML/JS, Kiro, coded prototypes | .NET, React, PostgreSQL |
| **Audience** | Designers, stakeholders | Business users, testers | End users, customers |
| **Questions Answered** | "Does it look good?" | "Does it work correctly?" | "Does it solve the problem?" |
| **Fidelity** | High visual fidelity | High functional fidelity | High everything |
| **Output** | Images, mockups, specs | Working HTML/code | Deployed application |
| **Backend** | None | None (mock data) | Real database, APIs |
| **Cost** | Low | Medium | High |
| **Time** | Hours to days | Hours to days | Weeks to months |

---

## Real-World Examples

### Example 1: CRM Dashboard

#### Design (Figma)

**Shows:**
- Layout of metric cards
- Chart placeholder with sample data
- Table with 3 sample rows
- Button styles and colors
- Navigation menu

**You can:**
- Click through screens
- See visual flow
- Appreciate aesthetics
- View different states

**You CANNOT:**
- Actually search customers
- Filter by status
- Export to CSV
- See real calculations
- Add new customers
- Edit existing data

#### Prototype (Kiro/HTML)

**Shows:**
- Same visual layout
- Working chart with 6 months of data
- Interactive table with 20 customers

**You can:**
- Type in search box and see results filter in real-time
- Click status dropdown and see table update
- Click export and download actual CSV file
- See metrics recalculate based on filters
- Add new customer and see it appear in list
- Edit customer and see changes reflected

**You CANNOT:**
- Connect to real database (it's mock data)
- Share data with other users
- Deploy to production
- Handle thousands of customers

#### Production (.NET + React + PostgreSQL)

**Shows:**
- Same visual layout
- Real-time data from database
- All features fully functional

**You can:**
- Everything from prototype, PLUS:
- Data persists across sessions
- Multiple users can collaborate
- Handles thousands of customers
- Integrates with other systems
- Secure authentication
- Performance optimized

---

### Example 2: RM Account Hierarchy

#### Design (Figma)

**Shows:**
- Tree structure layout
- Account cards design
- Favorite list panel
- Button styles
- Expand/collapse icons

**You can:**
- Click to see next screen
- View different states (expanded/collapsed)
- See visual hierarchy
- Appreciate the layout

**You CANNOT:**
- Expand/collapse tree nodes
- Select multiple accounts
- Create actual favorite list
- Generate report
- Search accounts
- See real hierarchy data

#### Prototype (Kiro/HTML)

**Shows:**
- Same tree structure
- Same visual design
- 22 accounts across 4 corporate groups

**You can:**
- Click to expand/collapse nodes
- Check boxes to select accounts
- Type name and create favorite list
- Click button and download report
- Search and see tree filter
- See selection count update
- View favorite lists
- Delete favorite lists

**You CANNOT:**
- Save to real database
- Share with other RMs
- Connect to actual banking system
- Handle thousands of accounts

#### Production (.NET + React + PostgreSQL)

**Shows:**
- Same functionality as prototype
- Real account data from banking system

**You can:**
- Everything from prototype, PLUS:
- Access real customer accounts
- Share favorite lists with team
- Data syncs with banking system
- Audit trail of all actions
- Role-based access control
- Performance with 10,000+ accounts

---

## The Spectrum of Fidelity

```
Low Fidelity ←────────────────────────────────────→ High Fidelity

Wireframe → Mockup → Design → Clickable → Functional → MVP → Production
                              Prototype   Prototype

Pen/Paper   Figma    Figma    Figma       Kiro/HTML   Code    Full Stack
(Sketches)  (Static) (Pretty) (Clicks)    (Works)     (Real)  (Deployed)

Hours       Hours    Days     Days        Days        Weeks   Months
$0          $0       $0       $0          $0          $$$     $$$$$
```

### Where Each Stage Fits:

1. **Wireframe** (Pen & Paper, Figma)
   - Rough sketches
   - Layout ideas
   - Very fast exploration

2. **Mockup** (Figma)
   - Detailed visuals
   - Pixel-perfect
   - Static images

3. **Design** (Figma)
   - Complete visual design
   - All screens
   - Design system
   - Brand guidelines

4. **Clickable Prototype** (Figma Prototype Mode)
   - Click through screens
   - Basic transitions
   - No real logic
   - Simulated flow

5. **Functional Prototype** (Kiro/HTML) ← **Focus of this project**
   - Working interactions
   - Business logic
   - Mock data that behaves realistically
   - Real validation and calculations

6. **MVP** (Production code with limited features)
   - Real backend
   - Real database
   - Core features only
   - Deployed to users

7. **Production** (Full application)
   - All features
   - Optimized performance
   - Security hardened
   - Scalable infrastructure

---

## Key Differences

### 1. Interactivity Level

**Design:**
```
Click button → Go to next screen
That's it.
```

**Prototype:**
```
Click button → Form validates → Shows error or success
Type in search → Results filter in real-time
Select items → Count updates → Enable/disable buttons
Complex workflows actually work
```

**Production:**
```
Everything from prototype, PLUS:
Data persists to database
Multi-user collaboration
Real-time updates
Integration with other systems
```

### 2. Data Handling

**Design:**
```
"John Doe" hardcoded in mockup
Same data on every screen
Can't change or manipulate
```

**Prototype:**
```
Mock data in JavaScript array
Can search, filter, sort
Can add, edit, delete
Calculations work
State changes persist (in session)
```

**Production:**
```
Real data from PostgreSQL database
Persists across sessions
Shared across users
Backed up and secure
Audit trail of changes
```

### 3. Business Logic

**Design:**
```
No logic
Just visual representation
"If this, then that" shown as separate screens
```

**Prototype:**
```
Customer tier calculation works
Validation rules enforced
Conditional display based on data
Workflows follow business rules
Error handling demonstrated
```

**Production:**
```
All business logic from prototype, PLUS:
Stored procedures in database
Transaction management
Concurrency handling
Performance optimized
Error recovery
```

### 4. Feedback Quality

**Design Feedback:**
- "Make the button bigger"
- "Change the color to blue"
- "Move this to the left"
- "I don't like this font"
- Visual preferences

**Prototype Feedback:**
- "The search doesn't find partial matches"
- "I can't select child accounts without parent"
- "The report is missing the total balance"
- "The workflow doesn't match our process"
- Functional issues

**Production Feedback:**
- "It's too slow with 10,000 customers"
- "I need to export to our ERP system"
- "Can we add role-based permissions?"
- "The mobile version needs work"
- Real-world usage issues

---

## When to Use What

### Use Design (Figma) When:

✅ Exploring visual concepts  
✅ Aligning on look & feel  
✅ Creating design system  
✅ Brand guidelines matter  
✅ Stakeholders care about aesthetics  
✅ Early in discovery phase  
✅ Need to present to executives  

**Questions it answers:**
- Does it look professional?
- Does it match our brand?
- Is the layout intuitive?
- Do stakeholders like the visual direction?
- Is it accessible and user-friendly?

**Time investment:** Hours to days  
**Cost:** Low (design tool subscription)  
**Output:** Visual mockups, design specs

---

### Use Prototype (Kiro/HTML) When:

✅ Validating functionality  
✅ Testing workflows  
✅ Demonstrating business logic  
✅ Getting user feedback on behavior  
✅ Proving technical feasibility  
✅ Handing off to developers  
✅ Requirements gathering  

**Questions it answers:**
- Does the workflow make sense?
- Can users accomplish their tasks?
- Does the business logic work correctly?
- Are there usability issues?
- Is this technically feasible?
- What are the edge cases?

**Time investment:** Hours to days  
**Cost:** Low (AI tool subscription)  
**Output:** Working HTML/JS code, requirements document

---

### Use Production Development When:

✅ Prototype is validated  
✅ Requirements are clear  
✅ Budget is approved  
✅ Need real backend  
✅ Need to deploy to users  
✅ Need scalability  
✅ Need security  

**Questions it answers:**
- Does it solve the real problem?
- Can it handle production load?
- Is it secure and compliant?
- Can we maintain it long-term?
- Does it integrate with our systems?

**Time investment:** Weeks to months  
**Cost:** High (development team)  
**Output:** Deployed application

---

## The Ideal Workflow

### Option A: Design-First (Traditional)

```
1. Design (Figma)
   ↓ Visual mockups, stakeholder approval
2. Prototype (Kiro)
   ↓ Make it functional, validate workflows
3. Development
   ↓ Build production version
4. Production
```

**Pros:** Beautiful visuals from the start  
**Cons:** Slower, two-step process, potential rework  
**Best for:** Projects where visual design is critical

---

### Option B: Prototype-First (Modern/Agile)

```
1. Prototype (Kiro)
   ↓ Functional with decent visuals
2. Review & Iterate
   ↓ Stakeholder approval on functionality
3. Polish (Optional Figma)
   ↓ Refine visuals if needed
4. Development
   ↓ Build production version
5. Production
```

**Pros:** Faster, validates functionality early, less rework  
**Cons:** Initial visuals less polished  
**Best for:** Projects where functionality validation is critical

---

### Option C: Hybrid (Best of Both)

```
1. Quick Design (Figma - 1 hour)
   ↓ Visual direction only
2. Prototype (Kiro)
   ↓ Functional, matching visual direction
3. Review Together
   ↓ Stakeholder approval on both
4. Development
   ↓ Build production version
5. Production
```

**Pros:** Good visuals + functionality, efficient  
**Cons:** Requires both tools  
**Best for:** Most projects with balanced needs

---

## Common Misconceptions

### Misconception 1: "Figma prototypes are functional"

**Reality:** Figma prototypes are clickable designs, not functional prototypes. They can simulate flow but can't execute business logic.

**Example:**
- Figma: Click "Search" → Shows pre-made screen with search results
- Functional: Type "Titan" → Actually filters 22 accounts to show only Titan group

### Misconception 2: "We can skip prototypes and go straight to development"

**Reality:** Building production code without validation is risky and expensive. Prototypes catch issues early when they're cheap to fix.

**Cost of change:**
- Prototype stage: $0 (just update HTML)
- Development stage: $1,000 (developer time to rework)
- Production stage: $10,000 (rework + testing + deployment + user impact)

### Misconception 3: "Prototypes are throwaway work"

**Reality:** Prototypes provide:
- Requirements documentation
- Business logic reference
- Code patterns for developers
- Validation of technical approach
- User feedback before expensive development

### Misconception 4: "Designs and prototypes serve the same purpose"

**Reality:** They serve different purposes:
- Design validates "Does it look right?"
- Prototype validates "Does it work right?"
- Both are valuable, but for different reasons

---

## Decision Matrix

### Choose Design (Figma) if:

- [ ] Visual appearance is the primary concern
- [ ] Stakeholders need to approve look & feel
- [ ] Creating a design system
- [ ] Team is comfortable with Figma
- [ ] Functionality is straightforward
- [ ] Budget is tight (design is cheaper than prototype)

### Choose Prototype (Kiro) if:

- [ ] Functionality needs validation
- [ ] Complex business logic involved
- [ ] Workflows need testing
- [ ] Handing off to developers
- [ ] Requirements are unclear
- [ ] Technical feasibility is uncertain
- [ ] Users need to test actual behavior

### Choose Both if:

- [ ] Visual design AND functionality both critical
- [ ] Budget allows for both
- [ ] Team has skills for both
- [ ] Timeline permits sequential approach
- [ ] Stakeholders need both types of validation

---

## Real-World Scenarios

### Scenario 1: E-commerce Checkout

**Design shows:**
- Beautiful form layout
- Button styles
- Error message placement

**Prototype demonstrates:**
- Form validation (email format, required fields)
- Credit card number formatting
- Address autocomplete
- Order total calculation
- Discount code application
- Error handling

**Why prototype matters:** Checkout flow has complex logic that needs validation before development.

---

### Scenario 2: Dashboard with Charts

**Design shows:**
- Chart layout and colors
- Metric card design
- Filter panel placement

**Prototype demonstrates:**
- Chart updates when filters change
- Metrics recalculate based on date range
- Export functionality works
- Drill-down interactions
- Loading states

**Why prototype matters:** Interactive dashboards have complex data relationships that need testing.

---

### Scenario 3: Multi-step Wizard

**Design shows:**
- Each step's layout
- Progress indicator
- Navigation buttons

**Prototype demonstrates:**
- Data carries forward between steps
- Validation at each step
- Can go back and edit
- Summary shows all entered data
- Conditional steps based on choices

**Why prototype matters:** Multi-step flows have state management that needs validation.

---

## Conclusion

**Design and Prototype are not competitors—they're complementary.**

**Design** answers: "Will users like how it looks?"  
**Prototype** answers: "Will users be able to use it?"  
**Production** delivers: "The actual solution users need"

**For most projects:**
- Start with prototype to validate functionality
- Add design polish if needed
- Build production version with confidence

**Remember the car analogy:**
- Design = Picture
- Prototype = Test drive
- Production = The car you buy

**Choose based on what you need to validate at each stage.** 🎯

---

## Further Reading

- [BA to Dev Workflow](../examples/BA-TO-DEV-WORKFLOW.md)
- [Prototype Standards](../.kiro/steering/prototype-standards.md)
- [Constitution vs System Prompts](./constitution-vs-system-prompts.md)
