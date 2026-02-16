---
name: realtime-event-integration-pattern
description: Integrates Domain Events with real-time socket broadcasting and cache invalidation using a Hexagonal Architecture. Must be explicitly enabled per project.
when_to_use:
  - project requires real-time updates (wearables, dashboards, TVs)
  - broadcasting domain state changes via WebSockets
  - cache invalidation triggered by domain events
  - synchronizing distributed clients
---

# Realtime Event Integration Pattern (Hexagonal + Octane Safe)

This pattern connects Domain Events to real-time socket broadcasting
and cache invalidation in a controlled, decoupled way.

⚠️ This pattern is OPTIONAL and must only be used if:
Realtime Capabilities are ENABLED in the project.

Projects without realtime requirements must NOT implement this pattern.

---

# Project Activation Rule

Before applying this pattern, confirm:

Realtime Capabilities: ENABLED

If disabled:
- Do not create broadcasters.
- Do not register socket listeners.
- Domain Events remain internal only.

---

# Core Principle

Domain must never depend on:

- WebSockets
- Redis Pub/Sub
- Broadcasting libraries
- Cache implementations

Domain emits events.
Infrastructure reacts to events.

---

# Architecture Flow

Domain Entity changes
    ↓
Domain Event recorded
    ↓
Application Action commits transaction
    ↓
Event dispatched (afterCommit)
    ↓
Infrastructure Listeners:
        1) Invalidate cache
        2) Broadcast socket message
        3) Trigger async jobs (optional)

Never emit sockets directly from:
- Controller
- Domain Entity
- Eloquent Model
- Middleware

---

# Step 1 — Domain Event (Pure PHP)

Location:

app/Modules/{ModuleName}/Domain/Events/

Rules:
- No Laravel dependencies.
- No Eloquent models.
- Payload contains IDs and minimal data.
- Immutable.
- Represents a meaningful business event.

Example use cases (Arvox):
- WorkoutSessionStarted
- WorkoutSessionFinished
- AthleteMetricUpdated
- AccessControlEntryRegistered

---

# Step 2 — Dispatch After Commit

If operation uses a transaction:

Events must be dispatched AFTER COMMIT.

Rules:
- Never dispatch before persistence completes.
- If transaction fails, event must not fire.
- Use afterCommit strategy.

This prevents:
- Broadcasting inconsistent state
- Emitting events for rolled-back operations

---

# Step 3 — Realtime Port (Hexagonal)

Create a Port for broadcasting.

Location:

app/Modules/{ModuleName}/Domain/Ports/Realtime/

Example interface:

BroadcasterInterface

Rules:
- Defines method like broadcast(EventPayload $payload).
- No implementation here.
- Domain never references implementation.

---

# Step 4 — Infrastructure Adapter (Only if Realtime Enabled)

Location:

app/Modules/{ModuleName}/Infrastructure/Realtime/

Example:

RedisSocketBroadcaster
PusherBroadcaster
ReverbBroadcaster
SoketiBroadcaster

Rules:
- Stateless.
- Octane-safe.
- No static properties.
- No in-memory state.
- Uses external service for broadcasting.

Never maintain persistent socket connections inside Laravel workers.

---

# Step 5 — Cache Invalidation Strategy

Cache invalidation must happen BEFORE broadcasting.

Order:

1) Invalidate affected cache keys.
2) Broadcast event.

Cache keys must:
- Include isolation root.
- Include sub-scope if applicable.
- Be deterministic.

Never:
- Invalidate cache inside Domain.
- Broadcast before invalidation.

---

# Step 6 — Listener Design

Listeners live in:

app/Modules/{ModuleName}/Infrastructure/Events/Listeners/

Responsibilities:
- Invalidate cache.
- Call BroadcasterInterface.
- Dispatch jobs if heavy processing required.

Listeners must:
- Be idempotent when possible.
- Be stateless.
- Avoid heavy synchronous I/O (prefer queue).

---

# Step 7 — Octane Safety Rules

Because Octane keeps workers alive:

- Do not store connection objects in static variables.
- Do not store per-request data in class properties.
- Do not memoize socket state.
- Use external pub/sub systems (Redis, etc.).
- Avoid in-memory broadcasting.

All realtime state must live outside the PHP process.

---

# Step 8 — Multi-Context Isolation

Realtime messages must include:

- Isolation Root ID
- Sub-scope ID if applicable

Never broadcast cross-tenant data.

Example channel naming:

root:{RootId}:module:event
root:{RootId}:sub:{SubScopeId}:module:event

Clients must subscribe only to their scope.

---

# Step 9 — Testing Requirements

Must test:

- Event is emitted only after successful commit.
- Cache invalidation occurs before broadcast.
- Broadcast includes correct scope identifiers.
- Cross-scope broadcast does not leak.
- No event emitted on rollback.

Prefer:
- Unit tests for listeners.
- Integration tests for event dispatch.
- Mock BroadcasterInterface.

---

# Security Considerations

- Never broadcast sensitive data directly.
- Prefer broadcasting IDs and minimal state.
- Clients should fetch fresh data via API if needed.
- Validate channel authorization at websocket layer.

---

# Prohibitions

- No socket emit in controllers.
- No broadcasting inside Domain Entities.
- No Eloquent broadcasting directly.
- No dispatch-before-commit.
- No cross-scope broadcasting.
- No static broadcaster instances.

---

# Output Requirements

When implementing realtime integration:

1) Provide Domain Event class.
2) Show Action dispatching event after commit.
3) Provide BroadcasterInterface (Port).
4) Provide Infrastructure Broadcaster implementation.
5) Provide Listener for cache invalidation + broadcast.
6) Show scoped channel naming.
7) Provide tests.
8) Ensure Octane-safe implementation.

Realtime must be decoupled, scoped, and transaction-safe.
Never sacrifice architectural boundaries for convenience.