# Architecture Decision Records (ADR)

## Purpose

This document captures key architectural decisions, their context, and rationale. Understanding the "why" behind our choices helps the team make consistent decisions and onboard new members effectively.

---

## ADR-001: .NET 9.0 for Backend API

**Status**: Accepted

**Context**:
We needed to choose a backend framework for building RESTful APIs with strong typing, performance, and enterprise support.

**Decision**:
Use .NET 9.0 with C# for backend API development.

**Rationale**:
- Modern, high-performance framework
- Strong typing with C# reduces runtime errors
- Excellent tooling and IDE support
- Built-in dependency injection
- Native async/await support
- Cross-platform (Linux, Windows, macOS)
- Long-term support from Microsoft
- Large ecosystem and community

**Consequences**:
- Team needs C# expertise
- Requires .NET runtime on servers
- Committed to Microsoft ecosystem
- Excellent performance and developer productivity

---

## ADR-002: React for Frontend

**Status**: Accepted

**Context**:
We needed a modern frontend framework for building interactive user interfaces with good developer experience and community support.

**Decision**:
Use React 18+ with TypeScript for frontend development.

**Rationale**:
- Component-based architecture promotes reusability
- Large ecosystem of libraries and tools
- Strong community support
- Virtual DOM for performance
- TypeScript adds type safety
- Excellent developer tools
- Easy to find developers with React experience
- Flexible - not overly opinionated

**Consequences**:
- Need to choose additional libraries (state management, routing)
- Requires build tooling (Vite/Webpack)
- Team needs React and TypeScript expertise
- Fast development with good maintainability

---

## ADR-003: PostgreSQL as Primary Database

**Status**: Accepted

**Context**:
We needed a reliable, scalable relational database with strong ACID guarantees and support for complex queries.

**Decision**:
Use PostgreSQL 15+ as the primary database.

**Rationale**:
- Open-source with no licensing costs
- ACID compliant with strong data integrity
- Excellent performance for complex queries
- Rich feature set (JSON support, full-text search, etc.)
- Strong stored procedure and function support
- Active development and community
- Proven scalability
- Better than MySQL for complex queries and data integrity

**Consequences**:
- Team needs PostgreSQL expertise
- Requires PostgreSQL-specific knowledge (vs generic SQL)
- Excellent reliability and feature set
- Lower total cost of ownership than commercial databases

---

## ADR-004: Stored Procedures for Business Logic

**Status**: Accepted

**Context**:
We needed to decide where to place database-related business logic: in application code or in the database.

**Decision**:
Implement business logic in PostgreSQL stored procedures and functions.

**Rationale**:
- Centralized business logic close to data
- Better performance (reduced network round-trips)
- Easier to maintain consistency across multiple applications
- Database-level validation and constraints
- Reusable across different application layers
- Easier to optimize database operations
- Clear separation of concerns

**Consequences**:
- Team needs strong SQL and PL/pgSQL skills
- Testing requires database environment
- More complex deployment (code + database changes)
- Potential vendor lock-in to PostgreSQL
- Better performance and data consistency

**Alternatives Considered**:
- ORM-only approach: Rejected due to performance concerns and complex business logic requirements
- Hybrid approach: Considered but adds complexity

---

## ADR-005: Authentik for Authentication

**Status**: Accepted

**Context**:
We needed an authentication solution that supports modern protocols, is secure, and can be self-hosted.

**Decision**:
Use Authentik as the identity provider with OAuth 2.0 / OpenID Connect.

**Rationale**:
- Open-source and self-hosted (data sovereignty)
- Supports OAuth 2.0 and OpenID Connect
- Modern, user-friendly interface
- Multi-factor authentication support
- Flexible user management
- Active development and community
- No per-user licensing costs
- Customizable authentication flows

**Consequences**:
- Need to host and maintain Authentik instance
- Team needs to learn Authentik configuration
- Full control over authentication system
- Lower long-term costs than SaaS solutions

**Alternatives Considered**:
- Auth0: Rejected due to cost and data sovereignty concerns
- Azure AD: Rejected due to vendor lock-in
- Custom solution: Rejected due to security complexity

---

## ADR-006: Liquibase for Database Migrations

**Status**: Accepted

**Context**:
We needed a database migration tool that supports version control, rollbacks, and works across environments.

**Decision**:
Use Liquibase for database schema migrations and versioning.

**Rationale**:
- Database-agnostic (though we use PostgreSQL)
- XML/YAML/SQL format support
- Built-in rollback support
- Tracks migration history in database
- Supports conditional migrations
- Enterprise-grade with good documentation
- Better suited for stored procedure management than Flyway

**Consequences**:
- Team needs to learn Liquibase syntax
- XML/YAML can be verbose
- Excellent migration control and rollback capabilities
- Clear audit trail of database changes

**Alternatives Considered**:
- Flyway: Rejected due to limited stored procedure support
- Entity Framework Migrations: Rejected due to stored procedure approach
- Manual SQL scripts: Rejected due to lack of version control

---

## ADR-007: Structured Logging with Serilog

**Status**: Accepted

**Context**:
We needed a logging solution that supports structured logging, multiple outputs, and easy integration with monitoring tools.

**Decision**:
Use Serilog for structured logging in .NET applications.

**Rationale**:
- Structured logging (JSON format)
- Multiple sinks (console, file, Application Insights, etc.)
- Easy to configure
- Excellent performance
- Rich ecosystem of extensions
- Semantic logging support
- Better than built-in .NET logging for production

**Consequences**:
- Additional dependency
- Team needs to learn Serilog configuration
- Excellent observability and debugging capabilities

---

## ADR-008: Separation of Concerns in Architecture

**Status**: Accepted

**Context**:
We needed to organize code in a maintainable, testable way that separates different responsibilities.

**Decision**:
Use layered architecture with clear separation:
- API layer (controllers, middleware)
- Application layer (services, business logic)
- Domain layer (entities, interfaces)
- Infrastructure layer (data access, external services)

**Rationale**:
- Clear separation of concerns
- Easier to test (can mock dependencies)
- Better maintainability
- Follows SOLID principles
- Industry best practice
- Easier to onboard new developers

**Consequences**:
- More files and folders
- Requires discipline to maintain boundaries
- Better long-term maintainability
- Easier to test and modify

---

## ADR-009: API Versioning in URL

**Status**: Accepted

**Context**:
We needed a strategy for API versioning to support backward compatibility.

**Decision**:
Include API version in URL path (e.g., `/api/v1/users`).

**Rationale**:
- Clear and explicit
- Easy to route and cache
- Visible in logs and monitoring
- Simple for clients to understand
- Industry standard approach

**Consequences**:
- URLs change with versions
- Need to maintain multiple versions
- Clear versioning strategy for clients

**Alternatives Considered**:
- Header-based versioning: Rejected due to less visibility
- Query parameter: Rejected due to caching issues

---

## ADR-010: Environment-Based Configuration

**Status**: Accepted

**Context**:
We needed a way to manage different configurations across development, UAT, and production environments.

**Decision**:
Use environment-specific configuration files with environment variables for secrets.

**Rationale**:
- Clear separation of environment configs
- Secrets not in source control
- Easy to override per environment
- Built-in .NET support
- Industry best practice

**Consequences**:
- Need to manage multiple config files
- Secrets management required (Key Vault)
- Secure and maintainable configuration

---

## ADR-011: PII Encryption at Database Level

**Status**: Accepted

**Context**:
We needed to protect Personally Identifiable Information (PII) in compliance with data protection regulations.

**Decision**:
Encrypt PII at the database column level using PostgreSQL's pgcrypto extension.

**Rationale**:
- Data encrypted at rest
- Transparent to application (with stored procedures)
- Centralized encryption logic
- Meets compliance requirements
- Keys managed separately from data

**Consequences**:
- Performance overhead for encryption/decryption
- Requires key management strategy
- Cannot easily search encrypted fields
- Strong data protection

---

## ADR-012: Test Pyramid Approach

**Status**: Accepted

**Context**:
We needed a testing strategy that balances coverage, speed, and maintainability.

**Decision**:
Follow the test pyramid: 70% unit tests, 20% integration tests, 10% E2E tests.

**Rationale**:
- Fast feedback from unit tests
- Integration tests catch component interaction issues
- E2E tests verify critical user journeys
- Balanced approach to test coverage
- Industry best practice

**Consequences**:
- Requires discipline to maintain ratio
- Need multiple testing frameworks
- Comprehensive test coverage with good performance

---

## Review and Updates

These decisions should be reviewed:
- When technology landscape changes significantly
- When requirements change
- Annually as part of architecture review
- When pain points emerge with current decisions

New ADRs should be added when making significant architectural decisions.
