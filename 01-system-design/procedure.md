# System Design Procedure

## 1. Understand the Problem

Before designing anything, clarify:

- What problem are we solving?
- Who are the users?
- What is the main workflow?
- What is in scope?
- What is out of scope?
- What are the success metrics?

Output:
- Problem statement
- User roles
- Main use cases
- Scope / non-scope

---

## 2. Define Requirements

### Functional Requirements

What the system must do.

Example:
- Users can create diagnostic sessions.
- Advisors can update job status.
- Managers can view all jobs.

### Non-Functional Requirements

How the system should behave.

Example:
- Response time under 200ms
- Secure authentication
- Audit logging
- Multi-tenant isolation
- Scalable to X users

Output:
- Functional requirements
- Non-functional requirements

---

## 3. Identify Core Entities

Define the main business objects.

Example:
- User
- Workshop
- Vehicle
- Job
- DiagnosticSession
- ScanJob

Output:
- Entity list
- Relationships
- Ownership rules

---

## 4. Design Main Workflows

Describe how the system behaves step by step.

Example:
- Create job
- Start diagnostic session
- Import fault codes
- Send customer update

Output:
- Workflow steps
- Edge cases
- Failure cases

---

## 5. Define Architecture

Choose the high-level structure.

Questions:
- Monolith or microservices?
- Backend modules?
- Frontend structure?
- External services?
- Background jobs?
- Message queues?

Output:
- Architecture diagram
- Main modules
- Dependency boundaries

---

## 6. Design API Contracts

Define endpoints or service interfaces.

For each API:

- Method
- Path
- Request body
- Response body
- Auth rules
- Error cases

Output:
- API contract

---

## 7. Design Data Model

Define database tables/collections.

For each table:

- Fields
- Types
- Relationships
- Indexes
- Constraints
- Soft delete or hard delete
- Audit fields

Output:
- ERD
- Schema draft

---

## 8. Security & Access Control

Define:

- Authentication
- Authorization
- Roles
- Tenant isolation
- Sensitive data
- Audit logs
- Rate limits

Output:
- Security rules
- RBAC matrix

---

## 9. Scalability & Performance

Think about:

- Expected users
- Read/write volume
- Indexing
- Caching
- Pagination
- Background jobs
- Queue usage
- File/storage strategy

Output:
- Performance assumptions
- Scaling plan

---

## 10. Failure Handling

Define what happens when things fail.

Examples:
- Payment provider down
- OBD adapter disconnected
- WhatsApp API fails
- Database timeout
- Duplicate request

Output:
- Retry rules
- Idempotency rules
- Error handling strategy

---

## 11. Trade-offs

Document why you chose one option over another.

Example:
- PostgreSQL instead of MongoDB
- Modular monolith instead of microservices
- JWT cookies instead of localStorage
- Queue instead of direct send

Output:
- Trade-off notes

---

## 12. Architecture Decision Records

For important decisions, create ADRs.

Format:

```markdown
# ADR-001: Use PostgreSQL as Primary Database

## Context
Why this decision is needed.

## Decision
What we decided.

## Alternatives
Other options considered.

## Consequences
Good and bad results of this decision.
