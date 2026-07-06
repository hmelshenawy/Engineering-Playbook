# Engineering Constitution

## 1. Design Before Coding

No implementation before clear requirements, scope, architecture, and tasks.

Required order:

PRD → Design → Spec → Tasks → Code

## 2. Documentation Is the Source of Truth

Code must follow the approved documentation.

If requirements are missing or unclear, update the docs before coding.

## 3. Keep Scope Small

Build only what is required now.

Reject feature creep unless it is explicitly approved.

## 4. Clean Layered Architecture

Responsibilities must stay separated:

* Controller: HTTP only
* Service: business logic
* Repository: database access
* DTO/schema: validation and contracts
* UI: rendering and user interaction only

No business logic in controllers or frontend.

## 5. Small Files and Small Functions

Code must stay readable and easy to review.

Targets:

* File: under 300 lines
* Function: under 30 lines
* One file = one clear responsibility
* One function = one clear action

If a file grows too large, split it.

## 6. No Spaghetti Code

Avoid:

* Mixed responsibilities
* Deep nested logic
* Large god files
* Duplicated logic
* Hidden side effects
* Random helper functions with no clear owner

Prefer simple, explicit, modular code.

## 7. Single Responsibility

Every module, service, component, and function should have one reason to change.

If something handles too many concerns, split it.

## 8. Backend Owns Business Logic

Business rules, permissions, ownership checks, validations, and critical workflows must live in the backend.

Frontend should not be trusted for business decisions.

## 9. Security by Default

Every protected action must validate:

1. Authentication
2. Authorization
3. Ownership / access scope
4. Input validation

Deny by default.

## 10. Explicit Error Handling

Errors must be clear and intentional.

Avoid:

* Silent failures
* Generic catch-all logic
* Swallowed exceptions
* Unclear error messages

## 11. Database Discipline

Before implementation, define:

* Main entities
* Relationships
* Query patterns
* Indexes
* Constraints
* Unique rules

Indexes are part of design, not an afterthought.

## 12. Idempotency and Retry Safety

Any operation that can be retried must not create duplicates.

Check:

* Worker retries
* API retries
* Double clicks
* Webhooks
* Queue jobs

Use unique constraints, idempotency keys, or state checks.

## 13. Dependency Discipline

Do not add libraries without a reason.

Before adding a dependency, check:

* Is it maintained?
* Is it necessary?
* Can it be replaced easily?
* Does it add complexity?
* Does it match the project architecture?

## 14. Testing Is Required

Every feature should include relevant tests.

At minimum:

* Unit tests for business logic
* Integration tests for API/data flows
* Regression tests for fixed bugs

No feature is complete without validation.

## 15. Comments Explain Why

Code should explain itself.

Comments are only needed for:

* Non-obvious business rules
* Security decisions
* Architecture decisions
* External limitations
* Performance trade-offs

Avoid comments that only explain what the code already says.

## 16. Simplicity First

Prefer boring, simple, maintainable solutions.

Do not introduce queues, microservices, caching, abstractions, or complex patterns unless the need is clear.

Complexity must be justified.
