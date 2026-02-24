---
name: laravel-cqrs-pattern
description: Implements Command Query Responsibility Segregation (CQRS) inside a Hexagonal Modular Monolith architecture.
when_to_use:
  - separating read and write logic
  - designing complex business operations
  - implementing heavy analytics queries
  - introducing caching to read models
  - refactoring large service classes
---

# CQRS Pattern (Hexagonal + Modular Monolith)

You are implementing CQRS (Command Query Responsibility Segregation)
inside a Laravel 12 Hexagonal Architecture project.

This project uses:

- Laravel 12
- PHP 8.4
- Modular Monolith
- Hexagonal Architecture
- Octane (FrankenPHP)
- Redis (optional for read caching)

CQRS must be applied pragmatically.
Do not over-engineer simple CRUD.

# Isolation Boundary Rule

Each module must explicitly define its isolation root entity.
All repository access must be scoped by that isolation identifier.
Policies must enforce isolation before permission checks.
Cache keys must include isolation identifier.

---

# Core Principle

Separate:

- Commands → Change state (Write operations)
- Queries → Read state (Read operations)

Commands must never return complex read models.
Queries must never modify state.

---

# Folder Structure

Inside module:

app/Modules/{ModuleName}/Application/UseCases/
    Commands/
    Queries/

app/Modules/{ModuleName}/Application/Handlers/

Optional:

app/Modules/{ModuleName}/Application/ReadModels/

---

# Step 1 — Commands (Write Side)

Commands represent intent.

Examples:

- CreateClaimCommand
- ApproveClaimCommand
- UpdateTrainingPlanCommand
- DeleteWorkoutCommand

Command rules:

- Immutable DTO.
- No business logic.
- Only carries data.
- Validated before reaching handler.

Handlers must:

- Receive Command.
- Load Entity via Repository Port.
- Apply domain rules.
- Persist changes.
- Handle transaction if needed.
- Invalidate cache if necessary.

Commands may return:
- Entity ID
- Minimal confirmation data
- Void

Commands must not build view models.

---

# Step 2 — Queries (Read Side)

Queries represent data retrieval.

Examples:

- GetClaimByIdQuery
- ListClaimsQuery
- GetDashboardMetricsQuery
- SearchTrainingPlansQuery

Query rules:

- Immutable DTO.
- No state modification.
- May include filters, pagination, sorting.

Handlers must:

- Fetch data via Repository Port.
- Optionally use cache port.
- Map to ReadModel or DTO.
- Never modify domain state.

Queries may use optimized read repositories.

---

# Step 3 — Read Models

Read Models are optional but recommended for complex queries.

Read Models:

- Represent projection of data for API response.
- May differ from Domain Entity.
- Are optimized for read use cases.
- May include computed fields.

Read Models must live in:

Application/ReadModels/

Never expose Eloquent models directly.

---

# Step 4 — Caching Rules

Caching is allowed only in Query Handlers.

Rules:

- Use Cache Port.
- Deterministic key.
- Explicit TTL.
- Explicit invalidation in related Command handlers.

Never cache in Controller.
Never cache inside Domain.

---

# Step 5 — Controller Responsibilities

Controller must:

- Validate request.
- Call Command or Query handler.
- Return ApiResponse.

Controller must not:

- Perform business logic.
- Access repositories directly.
- Mix command and query logic.

---

# Step 6 — Transactions

Transactions must be handled inside Command Handlers or Actions.

Never handle transactions in Controller.

---

# Step 7 — Domain Isolation

Domain must:

- Not know about CQRS explicitly.
- Remain pure.
- Expose behavior via methods.

CQRS lives in Application layer.

---

# Step 8 — Octane Safety

Because of Octane:

- Handlers must be stateless.
- No static mutable properties.
- No caching in memory.
- No request-specific state stored in properties.

All read caching must use Redis adapter.

---

# Step 9 — When NOT to Use CQRS

Do not use CQRS for:

- Simple CRUD with trivial logic.
- Small admin tables.
- Static reference data.

Use CQRS only when:

- Write logic differs significantly from read logic.
- Read models require aggregation.
- Caching is beneficial.
- Domain is complex.

---

# Step 10 — Testing Requirements

For Commands:

- Unit test handler.
- Domain rule test.
- Transaction behavior test.
- Cache invalidation test.

For Queries:

- Unit test handler.
- Cache hit/miss test.
- Multi-tenant isolation test.
- Performance-sensitive scenario test.

Feature tests must validate:

- Correct separation of behavior.
- Authorization rules.

---

# Prohibitions

- No state mutation inside Query.
- No heavy read logic inside Command.
- No Eloquent calls inside Controller.
- No direct cache usage in Controller.
- No mixing Command and Query responsibilities.
- No skipping Application layer.

---

# Output Requirements

When implementing CQRS:

1. Provide Command or Query class.
2. Provide corresponding Handler.
3. Provide repository port usage.
4. Provide controller integration.
5. Provide optional ReadModel.
6. Provide cache integration if applicable.
7. Provide tests.
8. Ensure Octane safety.

Prefer clarity over abstraction.
Do not introduce unnecessary complexity.
Follow hexagonal boundaries strictly.