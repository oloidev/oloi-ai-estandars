---
name: domain-event-pattern
description: Implements Domain Events in a Hexagonal Architecture to decouple side effects from business logic, with Octane-safe and transaction-safe delivery.
when_to_use:
  - decoupling side effects from core domain logic
  - triggering workflows after state changes
  - integrating jobs/notifications without coupling services
  - ensuring clean boundaries for complex operations
  - enabling eventual consistency patterns
---

# Domain Event Pattern — Hexagonal + Transaction Safe

You are implementing Domain Events in a Laravel 12 Hexagonal Architecture project.

This project uses:
- Laravel 12
- PHP 8.4
- Octane (FrankenPHP)
- Modular Monolith + Hexagonal Architecture
- Redis (optional)
- Jobs/Queues (optional)

Domain Events help keep the Domain pure while allowing side effects to happen outside it.

---

# Core Concept

A Domain Event is a record that "something meaningful happened" in the domain.

Examples:
- ClaimSubmitted
- ClaimApproved
- TrainingPlanPublished
- MembershipUpgraded

Rules:
- Domain raises events (or records them).
- Infrastructure reacts to events (listeners/subscribers).
- Domain Events must not depend on Laravel.

---

# Step 1 — Location

Domain Events must live in:

app/Modules/{ModuleName}/Domain/Events/

They must be pure PHP:
- No Laravel Facades
- No Container
- No Eloquent models
- No Queue interfaces

---

# Step 2 — Event Payload Rules

Event payload must contain only:
- Value Objects
- Scalar identifiers (IDs)
- Minimal data required for downstream handling

Never include:
- Eloquent models
- Full aggregates unless necessary
- Request objects
- Auth user objects

Prefer IDs and re-load data in listeners if needed.

---

# Step 3 — Raising Events

There are two recommended approaches:

A) Entity records events (preferred for rich domain)
- Entity has a private list of events
- Methods push events when state changes
- Application layer collects and dispatches

B) Action/Handler emits events after a successful operation (pragmatic)
- After domain operation + persistence, emit events explicitly

Either approach is acceptable, but events must remain domain-level (framework-free).

---

# Step 4 — Dispatching Events (Application → Infrastructure)

Domain should not dispatch events directly using Laravel.

Dispatching must happen in:
- Application Actions/Handlers (after successful persistence)
- Infrastructure Event Bus adapter

If transactions are used:
- Ensure events are dispatched after commit (afterCommit strategy).
- Do not dispatch events before a transaction commits.

---

# Step 5 — Listeners/Subscribers (Infrastructure)

Listeners live in:

app/Modules/{ModuleName}/Infrastructure/Events/Listeners/

They may:
- Dispatch jobs
- Send notifications
- Call external services via Ports
- Invalidate cache
- Update projections/read models

Listeners must remain stateless and Octane-safe.

---

# Step 6 — Idempotency

Event handling must be safe to retry when possible.

Rules:
- Prefer idempotent listeners.
- Use unique identifiers for deduplication if needed.
- Avoid duplicate side effects (double emails, double invoices).

---

# Step 7 — Performance & Octane Safety

- Do not store event state in static properties.
- Avoid heavy synchronous listeners; prefer jobs for expensive tasks.
- Use queues for slow I/O.

---

# Step 8 — Testing Requirements

Tests must cover:
- Event is recorded/emitted on the correct domain transition.
- Event is dispatched only after successful persistence.
- Listener behavior (unit/integration depending on complexity).
- Idempotency behavior if applicable.

Prefer:
- Unit tests for Domain event emission
- Integration tests for listener wiring

---

# Prohibitions

- No Laravel dependencies in Domain Events.
- No dispatching Laravel events from Domain layer.
- No Eloquent models inside event payload.
- No side effects inside Domain Entities.
- No dispatch-before-commit for transactional writes.

---

# Output Requirements

When implementing Domain Events:
1) Provide Domain Event class (pure PHP)
2) Show how/where the event is raised (Entity or Action)
3) Provide dispatch mechanism (Action/Infrastructure adapter)
4) Provide Infrastructure Listener(s)
5) Provide after-commit strategy if transactions exist
6) Provide tests for emission + handling
7) Ensure Octane-safe, stateless execution

Always keep Domain pure and side effects decoupled.
Prefer clarity and explicitness over magic behavior.