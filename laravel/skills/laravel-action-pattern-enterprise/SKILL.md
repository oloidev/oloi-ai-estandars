---
name: laravel-action-pattern-enterprise
description: Implements enterprise-grade Actions for complex business operations with transactions, side effects coordination, and Octane-safe practices in a Hexagonal Modular Monolith.
when_to_use:
  - implementing complex workflows (multi-step operations)
  - coordinating multiple repositories/services
  - handling transactions safely
  - orchestrating side effects (events, notifications, jobs)
  - refactoring large service methods
---

# Action Pattern (Enterprise) — Hexagonal + Octane Safe

You are implementing an Action in a Laravel 12 Hexagonal Architecture project.

This project uses:
- Laravel 12
- PHP 8.4
- Octane (FrankenPHP)
- Sanctum
- Spatie Permission
- Modular Monolith + Hexagonal Architecture
- ApiResponse helper
- Redis (optional)

Actions represent a single business use case / operation and orchestrate Domain + Ports + Adapters.

---

# Core Rules

- Actions live in the Application layer.
- Actions orchestrate: validation (already done), authorization (already done), domain logic, persistence, side effects.
- Actions must be stateless and Octane-safe.
- Actions should be atomic, reusable, and testable.

Controllers must only call Actions/Handlers and return ApiResponse.

---

# Step 1 — Location

Actions must live in:

app/Modules/{ModuleName}/Application/Actions/

If using CQRS:
- Commands handled by Actions/Handlers (write side)
- Queries handled by Query Handlers (read side)

---

# Step 2 — When to Use an Action

Use an Action when:
- The operation spans multiple repositories.
- A transaction is required.
- There are side effects (events/jobs/notifications).
- Business rules are non-trivial.
- You need a clean unit-testable boundary for a use case.

Do NOT create Actions for trivial CRUD.

---

# Step 3 — Action Interface

Action input should be a Command/DTO (immutable).

Action output should be one of:
- Domain Entity (or ID)
- Result DTO
- Void (if appropriate)

Never return Eloquent models from Actions.

---

# Step 4 — Transaction Handling

Transactions must be handled inside Actions when needed.

Rules:
- Use explicit transactions for multi-write operations.
- Ensure side effects are triggered only after successful commit.
- If using events/jobs, prefer afterCommit behavior.

Do not handle transactions in controllers.

---

# Step 5 — Side Effects Coordination

Side effects include:
- Domain events dispatch
- Jobs/queues
- Notifications/emails
- Cache invalidation
- External API calls

Rules:
- Prefer emitting Domain Events from Domain/Action and handling them in Infrastructure/Listeners.
- Cache invalidation must be explicit and scoped.
- External HTTP calls should go through Ports (interfaces) and adapters.

---

# Step 6 — Error Handling

Actions must:
- Throw Domain Exceptions for domain rule violations.
- Throw Application Exceptions for orchestration failures.
- Never leak infrastructure exceptions directly to controllers.

Controllers must translate errors to ApiResponse::error() consistently.

---

# Step 7 — Octane Safety

Because Octane keeps workers alive:
- No static mutable properties.
- No storing request/user objects in class properties beyond method scope.
- No in-memory memoization.
- Keep Actions stateless.

---

# Step 8 — Testing Requirements

Actions must have unit tests covering:
- Happy path
- Domain rule violations
- Transaction behavior (commit/rollback)
- Side effects triggered/not triggered
- Cache invalidation (if applicable)

Prefer mocking Ports for external systems.

---

# Prohibitions

- No Eloquent in Domain.
- No Eloquent models returned from Actions.
- No side effects in controllers.
- No cache invalidation in controllers.
- No hidden transactions.
- No shared mutable state.

---

# Output Requirements

When generating an Action:
1) Provide Command/DTO
2) Provide Action class
3) Show repository ports used
4) Show transaction usage (if needed)
5) Show side effects strategy (events/jobs/cache)
6) Provide unit tests
7) Ensure Octane-safe implementation

Always preserve existing functionality and architecture boundaries.
Prefer explicit orchestration over hidden magic.