# AI Engineering Constitution

A collection of steering documents (constitution files) that define engineering standards, architectural decisions, and development practices. These documents are designed to work with AI-assisted development tools like Kiro, ensuring consistent, high-quality code generation.

## What Are Steering Documents?

Steering documents are markdown files that act as your team's engineering constitution. When working with AI coding assistants, these documents are automatically included as context, ensuring every design, task, and line of code follows your established standards.

## Repository Structure

```
.kiro/steering/
├── api-standards.md              # .NET 9.0 API development standards
├── ui-standards.md               # React + TypeScript UI standards
├── database-standards.md         # PostgreSQL + Liquibase database standards
├── security-standards.md         # Security, authentication, PII protection
├── deployment-standards.md       # CI/CD pipelines and deployment processes
├── quality-standards.md          # Testing, QA automation, PEN testing
├── logging-standards.md          # Structured logging standards
├── architecture-decisions.md     # Architecture Decision Records (ADRs)
└── prototype-standards.md        # HTML5 + JS prototyping for BAs/UX
```

## Two Use Cases

### 1. Production Development Standards

**For**: Development teams building production applications

**Tech Stack**:
- Backend: .NET 9.0 with C#
- Frontend: React 18+ with TypeScript
- Database: PostgreSQL 15+ with stored procedures
- Auth: Authentik (OAuth 2.0 / OIDC)
- Migrations: Liquibase
- Testing: xUnit, Jest, Playwright

**Documents**:
- `api-standards.md`
- `ui-standards.md`
- `database-standards.md`
- `security-standards.md`
- `deployment-standards.md`
- `quality-standards.md`
- `logging-standards.md`
- `architecture-decisions.md`

### 2. BA/UX Prototyping Standards

**For**: Business Analysts and UX designers creating functional prototypes

**Tech Stack**:
- HTML5 + CSS3
- Vanilla JavaScript (ES6+)
- Tailwind CSS (via CDN)
- Chart.js for visualizations
- No build tools required

**Documents**:
- `prototype-standards.md`

**Example Use Case**: Create a beautiful CRM dashboard prototype in a single HTML file that BAs can build while sitting with business stakeholders, then hand off to dev team with clear requirements.

## How to Use

### With Kiro IDE

1. Clone this repository or copy the `.kiro/steering/` folder to your project
2. Kiro automatically loads these documents as context
3. When you create specs or ask for code, Kiro follows these standards automatically

### With Other AI Tools

1. Copy relevant steering documents to your project
2. Reference them in your prompts: "Follow the standards in .kiro/steering/api-standards.md"
3. Or paste relevant sections into your AI conversation

### For Your Team

1. **Review with SMEs**: Have domain experts review their respective documents
2. **Customize**: Adapt to your specific tech stack and practices
3. **Iterate**: Update as you learn what works
4. **Onboard**: New team members read these to understand your standards

## Example: Creating a CRM Dashboard Prototype

Using the `prototype-standards.md` constitution, you can prompt an AI:

```
Create a beautiful, functional CRM dashboard prototype using HTML5, Tailwind CSS, 
and vanilla JavaScript following the prototype-standards.md guidelines.

Include:
- Key metrics cards (Revenue, Customers, Deals, Avg Deal Size)
- Revenue trend chart (last 6 months)
- Customer list with search/filter
- Customer detail modal
- Add customer functionality
- Mock data for 20 customers
- Single HTML file with CDN libraries

Make it professional and suitable for executive presentation.
```

The AI will generate a complete, beautiful prototype following all the standards.

## Key Features

### Domain Separation
Each standard is in its own file for:
- Parallel SME reviews
- Focused context
- Clear ownership
- Easier maintenance

### Architecture Decision Records (ADRs)
The `architecture-decisions.md` file captures the "why" behind technology choices:
- Why .NET 9.0?
- Why stored procedures?
- Why Authentik for auth?
- Why Liquibase over Flyway?

This helps AI (and humans) understand context and make better decisions.

### Security First
Comprehensive security standards including:
- PII protection and encryption
- Authentication and authorization
- Input validation and sanitization
- Penetration testing requirements
- Compliance and audit trails

### Quality Gates
Built-in quality standards:
- 80% code coverage minimum
- Test pyramid approach (70% unit, 20% integration, 10% E2E)
- Automated security scans
- Performance benchmarks

## Benefits

1. **Consistency at Scale**: Every feature follows the same standards
2. **Faster Onboarding**: New developers learn by osmosis
3. **Living Documentation**: Actively used, stays relevant
4. **Reduced Decision Fatigue**: Big decisions made once
5. **Built-In Quality**: Security and testing standards automatic
6. **AI-Native Development**: AI assistants know your standards

## Customization

These documents are templates. Customize them for your needs:

1. **Update Tech Stack**: Change .NET to Java, React to Vue, etc.
2. **Add Standards**: Create new documents for your specific needs
3. **Remove Sections**: Delete what doesn't apply
4. **Add Examples**: Include your own code examples
5. **Update ADRs**: Document your architectural decisions

## Contributing

Found an improvement? Have a suggestion?

1. Fork this repository
2. Make your changes
3. Submit a pull request
4. Share your learnings

## Real-World Usage

These standards are used in production by teams building:
- Enterprise CRM systems
- Healthcare applications
- Financial services platforms
- E-commerce solutions

## Learn More

Read the full story of how we built this:
- **Medium Article**: [Link to your Medium article]
- **LinkedIn**: [Link to your LinkedIn post]

## License

MIT License - Feel free to use and adapt for your team.

## Questions?

Open an issue or reach out on LinkedIn.

---

**Built with ❤️ for AI-native development**
