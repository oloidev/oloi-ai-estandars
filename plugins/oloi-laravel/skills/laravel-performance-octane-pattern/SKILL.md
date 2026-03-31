---
name: laravel-performance-octane-pattern
description: Enforces Octane-safe, high-performance Laravel 12 practices (FrankenPHP) including stateless services, safe caching, DB/query optimization, and memory-leak prevention in a Hexagonal Modular Monolith.
when_to_use:
  - optimizing performance under Laravel Octane
  - reviewing code for Octane safety (state leakage, memory leaks)
  - building high-throughput endpoints (dashboards, realtime, analytics)
  - introducing caching, queues, events, or heavy queries
  - refactoring services/controllers for production scale
---

# Performance + Octane Pattern (Laravel 12 + FrankenPHP)

You are working on a Laravel 12 API running on Octane (FrankenPHP).
Octane keeps the application worker alive between requests.

This pattern prevents state leakage, memory growth, and slow endpoints.

This project uses:
- Laravel 12
- PHP 8.4
- Octane (FrankenPHP)
- Redis (optional)
- MySQL/MariaDB
- Modular Monolith + Hexagonal Architecture
- ApiResponse helper

---

# Core Rule

Assume the PHP process will handle many requests.

Anything you store in memory can persist across requests.

Therefore:
- Services must be stateless.
- No request/user objects stored beyond method scope.
- No static mutable properties.
- No per-request caches stored in singletons.

---

# Step 1 — Stateless Services & Handlers

All Application Services / Actions / Handlers must be stateless.

Allowed:
- Constructor injection of Ports (interfaces)
- Pure computations
- Local variables inside methods

Not allowed:
- Storing Request/User in class properties
- Storing computed results as properties for reuse
- Storing arrays/maps as internal caches

If repeated caching is needed:
- Use Redis cache via Cache Port (external state).

---

# Step 2 — Forbid Static Mutable State

Never use:
- static properties that change
- global variables for caching
- singleton services with mutable internal state

Allowed:
- static constants
- enums
- readonly immutable configuration

If you find static mutable state:
- refactor to Redis cache or request-scoped local variables.

---

# Step 3 — Avoid Request Scope Leakage

Never store these outside the method:
- Illuminate\Http\Request
- Auth user model
- Tenant/isolation context object
- Large collections/results

Derive needed scalars (IDs) once and pass through DTOs.

---

# Step 4 — Caching Strategy (External Only)

Use caching only through the cache Port (Hexagonal) and Redis adapter.

Rules:
- Cache only read-side (Query handlers)
- Always include isolation root and filters in key
- Always define TTL
- Always implement invalidation on writes
- Never cache Eloquent models directly (map to DTO/ReadModel)

Never:
- Cache::remember() in Controller
- in-memory memoization in singletons
- caching data that depends on auth/role without key isolation

---

# Step 5 — Query Performance (DB)

All data access must:
- avoid N+1 queries
- eager load required relations
- paginate large lists
- select only needed columns when possible
- use indexes for foreign keys and filter columns

Repository Adapters must:
- use with()/withCount() appropriately
- avoid loading unnecessary relations
- avoid unbounded queries (no ->get() on huge sets)
- avoid returning Eloquent Models to Application layer

---

# Step 6 — Heavy Endpoints: Budget & Decomposition

If an endpoint is heavy (dashboard/analytics/realtime):
- implement as Query (CQRS)
- use caching
- split into smaller queries if needed
- compute aggregates in DB when possible
- consider background jobs for expensive computations

Prefer:
- pre-aggregations
- read models
- cache warming (optional)

Avoid:
- looping in PHP over massive datasets
- repeated queries inside loops

---

# Step 7 — Serialization & Payload Size

Return only what the client needs.

Rules:
- Use Resources or ReadModels to shape response
- Avoid returning full nested objects unless required
- Consider pagination for lists
- Avoid huge arrays in memory

---

# Step 8 — Events, Jobs, and Realtime Under Octane

Events/listeners/jobs must be:
- stateless
- idempotent when possible
- afterCommit for transactional writes
- avoid heavy synchronous IO (prefer queue)

Realtime broadcasting must:
- not keep socket connections in PHP worker
- use external pub/sub / websocket server
- not store broadcaster state in memory

---

# Step 9 — External HTTP Calls

External calls must:
- be behind Ports (interfaces)
- have timeouts
- be retriable where safe
- avoid blocking critical request path if possible

For slow I/O:
- prefer jobs/queues
- or cached responses (read-only)

---

# Step 10 — Memory & Leak Prevention Checklist

When reviewing code, flag:
- static mutable properties
- singletons storing arrays/results
- services accumulating data across calls
- large collections stored on objects
- global caches not cleared

Prefer:
- local variables
- streaming/pagination
- Redis cache for reuse

---

# Step 11 — Concurrency Safety

Because Octane increases concurrency:
- avoid relying on shared mutable memory
- use DB transactions for consistency
- use row-level locking if necessary (select ... for update) inside Actions
- avoid race conditions in counters/aggregates (use DB atomic ops)

---

# Step 12 — Instrumentation (Minimal)

At minimum:
- log slow endpoints (duration)
- add correlation ID support if available
- include isolation root ID in logs (when safe)

Do not log sensitive data.

---

# Testing Requirements (Performance + Safety)

When applying this pattern, add tests for:
- repository does not trigger N+1 (where feasible)
- caching returns consistent results per scope
- invalidation works after writes
- heavy endpoints complete within acceptable time (optional integration test)

---

# Prohibitions

- No in-memory caching inside singletons
- No static mutable state
- No storing Request/Auth user in properties
- No unbounded DB queries
- No N+1 queries
- No caching in Controllers

---

# Output Requirements

When optimizing for Octane performance, produce:

1) Octane safety review (what to avoid in current code)
2) Repository query optimizations (eager loading, indexes, pagination)
3) Cache plan (keys, TTL, invalidation, scope isolation)
4) Any required refactors to stateless services
5) Tests covering cache + isolation + correctness
6) Ensure all changes preserve existing functionality

Always preserve architecture boundaries and avoid breaking changes.
Prefer explicit, deterministic performance improvements over clever hacks.