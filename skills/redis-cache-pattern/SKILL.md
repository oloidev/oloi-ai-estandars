---
name: redis-cache-pattern
description: Implements Redis caching for heavy read operations following Hexagonal Architecture and Octane-safe principles.
when_to_use:
  - optimizing heavy queries
  - caching dashboard endpoints
  - improving performance for read models
  - reducing repeated database load
---

# Redis Cache Pattern (Hexagonal + Octane Safe)

You are implementing caching using Redis in a Laravel 12 Hexagonal Architecture project.

This project uses:

- Laravel 12
- PHP 8.4
- Octane (FrankenPHP)
- Redis
- Modular Monolith
- Hexagonal Architecture

Caching must follow Ports & Adapters principles.

---

# Core Rule

Redis is Infrastructure.

Domain and Application layers must not depend on Redis directly.

Always use a Cache Port (interface).

Never use Cache:: or Redis:: directly inside Controllers or Domain.

---

# Step 1 — Confirm This Is a Query

Caching is allowed only for:

- Read operations (Queries)
- Heavy computations
- Aggregated dashboards
- External API calls

Do NOT cache:

- Commands (write operations)
- Mutable transactional operations
- Security-sensitive responses
- User-specific request-scoped data without proper key isolation

---

# Step 2 — Define Cache Port (Domain Layer)

Under:

app/Modules/{ModuleName}/Domain/Ports/Cache/

Create:

{ModuleName}CacheRepository (or specific cache interface)

Example methods:

- getById(string $id): ?Entity
- remember(string $key, int $ttl, callable $callback): mixed
- forget(string $key): void

Domain defines contract only.
No Redis implementation here.

---

# Step 3 — Implement Redis Adapter (Infrastructure Layer)

Under:

app/Modules/{ModuleName}/Infrastructure/Cache/Redis/

Create:

Redis{ModuleName}CacheRepository

Rules:

- Use Laravel Cache repository abstraction.
- Do not use static properties.
- Do not store request state.
- Ensure Octane-safe behavior.

Use tagged cache if invalidation groups are required.

Example strategy:

Cache::tags(['module', 'entity'])->remember(...)

---

# Step 4 — Apply Caching in Application Layer

Caching must be applied in:

Application/Handlers (Query handlers only)

Example pattern:

- Try cache.
- If cache miss → fetch from repository.
- Store result with TTL.
- Return cached result.

Never apply caching inside Controller.

---

# Step 5 — Cache Key Design Rules

Keys must be:

- Deterministic
- Unique per filter combination
- Namespaced by module

Format:

module:{ModuleName}:{Operation}:{HashOfFilters}

If multi-tenant:

Include tenant_id or company_id in key.

Never use raw request input as key.
Hash complex filters if necessary.

---

# Step 6 — TTL Strategy

TTL must be explicitly defined.

Recommended guidelines:

- Reference data: 1–24 hours
- Dashboard analytics: 5–15 minutes
- Frequently changing data: 1–5 minutes
- Heavy static computations: configurable

Never use unlimited TTL unless explicitly required.

---

# Step 7 — Cache Invalidation Rules

Write operations (Commands) must:

- Explicitly invalidate affected cache keys.
- Use tags when possible for grouped invalidation.
- Never rely on implicit expiration only.

Invalidation must happen in:

Application layer after successful persistence.

Never invalidate in Controller.

---

# Step 8 — Octane Safety

Because Octane keeps workers alive:

- Do not store cached values in static properties.
- Do not memoize results in class properties.
- Always use Redis or Laravel cache store.
- Avoid per-request memory caching inside singletons.

Cache must be external (Redis), not in-memory per worker.

---

# Step 9 — Testing Cache Behavior

Tests must cover:

- Cache hit scenario.
- Cache miss scenario.
- Cache invalidation after write.
- Multi-tenant key isolation.
- Correct TTL behavior (mock time if necessary).

Unit tests should mock cache port.
Feature tests may use Redis testing environment.

---

# Step 10 — Optional Advanced Patterns

Allowed advanced patterns:

- Cache tags for grouped invalidation.
- Read Model caching (CQRS).
- Cache warming jobs.
- Background refresh pattern.

Not allowed:

- Direct Cache facade usage in Domain.
- Global state caching.
- Silent cache failures.
- Caching raw Eloquent models without mapping.

---

# Security Considerations

Never cache:

- User tokens
- Sensitive personal data without encryption
- Per-user authorization-sensitive responses without proper key isolation

Always isolate cache by tenant and role when necessary.

---

# Prohibitions

- No Cache:: in Controllers.
- No Redis:: in Domain.
- No static caching.
- No ignoring invalidation.
- No long-lived in-memory state (Octane risk).

---

# Output Requirements

When implementing Redis caching:

1. Provide Cache Port interface.
2. Provide Redis adapter implementation.
3. Modify Query Handler to use cache.
4. Provide invalidation logic in corresponding Command handler.
5. Provide TTL explanation.
6. Provide test examples.
7. Ensure Octane safety.

Always preserve existing architecture.
Never remove unrelated functionality.
Prioritize explicit cache invalidation over magic behavior.