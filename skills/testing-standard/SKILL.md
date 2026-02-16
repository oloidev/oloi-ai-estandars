---
name: testing-standard
description: Creates complete Laravel 12 test coverage (Feature + Unit) for Hexagonal Modules, including Policies, Requests, CQRS Handlers, Actions, Events, Cache, and Multi-Context Isolation.
when_to_use:
  - creating tests for a new endpoint/module
  - adding missing coverage to an existing feature
  - verifying authorization, validation, isolation, and caching
  - preventing regressions during refactors (hexagonal/CQRS)
  - ensuring Octane-safe behavior via deterministic tests
---

# Testing Standard (Laravel 12 + Hexagonal + Octane)

You are implementing tests in a Laravel 12 API using:

- PHP 8.4
- Octane (FrankenPHP)
- Sanctum
- Spatie Permission
- Modular Monolith + Hexagonal Architecture
- CQRS (Commands/Queries)
- Actions pattern
- Domain Events
- Redis caching (optional)
- ApiResponse helper

Tests must be reliable, deterministic, and scoped (multi-context isolation safe).

---

# Core Goals

Every feature must be protected by tests for:

1. Authorization (Policies + permissions)
2. Validation (FormRequests)
3. Isolation (root + sub-scope boundaries)
4. Business behavior (Handlers/Actions)
5. API contract (ApiResponse + Resources)
6. Side effects (Events/Jobs/Cache invalidation)
7. Backward compatibility (API versioning when applicable)

---

# Test Types Required

## A) Feature Tests (HTTP)

Location (recommended):
tests/Feature/Api/V{n}/{ModuleName}/

Must cover:

- 200/201 success response + ApiResponse structure
- 401 unauthenticated
- 403 unauthorized (policy)
- 422 validation errors
- 404 not found (scoped)
- Pagination and filters for lists
- Version-specific contracts if using /v1, /v2

Feature tests must assert:

- JSON structure: success, message, data/errors
- Resource shape (fields, types, optional fields)
- No sensitive leakage

---

## B) Unit Tests (Application)

Location:
tests/Unit/Modules/{ModuleName}/Application/

Must cover:

- Command/Query Handlers behavior
- Actions orchestration behavior
- Cache hit/miss behavior in Query handlers (mocked port)
- Cache invalidation after Commands/Actions
- External services via Ports (mocked)

Rules:

- Unit tests must NOT hit DB unless explicitly needed.
- Mock repository ports and cache ports.

---

## C) Policy Tests

Location:
tests/Unit/Modules/{ModuleName}/Policies/

Must cover:

- Allowed when permission granted + scope valid
- Denied when permission missing
- Denied on cross-root access
- Denied on cross-sub-scope access (when applicable)
- Allowed for super-admin (when applicable)

Rules:

- Do not test policies indirectly only via Feature tests.
- Must have direct policy tests for correctness.

---

## D) Request Validation Tests

Location:
tests/Unit/Modules/{ModuleName}/Requests/

Must cover:

- required fields
- type validation
- boundary validations
- authorization() behavior (if non-trivial)

Rules:

- Prefer testing FormRequest rules and messages in isolation
  OR assert 422 from Feature tests for key cases.

---

## E) Repository Adapter Integration Tests (Optional but Recommended)

Location:
tests/Integration/Modules/{ModuleName}/Infrastructure/

Must cover:

- scoping is enforced in queries (root + sub-scope)
- eager loading prevents N+1 (where feasible)
- pagination limits

Rules:

- Only integration tests should hit DB for repository adapter behavior.
- Use RefreshDatabase.

---

# Multi-Context Isolation Test Requirements

For any isolated module, tests must include:

1. Cross-root attempt:

- User in root A cannot access entity from root B → 404 or 403 based on your standard
  (prefer 404 to avoid leaking existence).

2. Cross-sub-scope attempt (if membership restricted):

- User with Practice 1 cannot access Practice 2 in same root.

3. Cache isolation:

- Cache keys must include root id (and sub-scope if applicable).
- Cached response for root A must never serve root B.

---

# Cache Test Requirements (If Redis Cache Pattern Used)

Query Handler tests must cover:

- cache miss calls repository and stores result (mock cache port)
- cache hit returns cached result without repository call
- invalidation triggered by related command/action (mock cache port forget/tags)

Rules:

- Prefer mocking Cache Port for unit tests.
- Use Redis integration tests only if needed.

---

# Domain Events & Realtime (If Enabled)

## Domain Event Tests

- Event recorded/emitted on correct domain transition
- Event dispatched after successful persistence (afterCommit)
- No event dispatched on rollback/failure

## Realtime Listener Tests (Arvox realtime only)

- Cache invalidation happens before broadcast
- Broadcast payload includes scope IDs
- Listener is idempotent when possible
- Broadcaster Port is mocked

---

# API Versioning Tests (If Used)

When V2 exists:

- V1 tests must remain unchanged and continue passing.
- V2 tests must assert new contract.
- Ensure no accidental changes to V1 resources.

---

# Test Data & Factories

Rules:

- Use factories for Eloquent models.
- Avoid hardcoding IDs unless required.
- Create helper builders for scope setup:
  - createRootScope()
  - createSubScope()
  - createUserWithPermissions()
  - attachUserToSubScopes()

Prefer clear test setup over cleverness.

---

# ApiResponse Assertions

All Feature tests must assert standardized response:

success: boolean
message: string
data: object|null
errors: array|null

For errors:

- 401/403/404 must have success=false
- 422 must include errors with field keys

---

# Performance Safety in Tests

Tests must:

- avoid brittle timing assertions
- avoid flakey dependencies
- not rely on test order
- reset state using RefreshDatabase where DB involved
- avoid static mutable state

---

# Prohibitions

- No tests that only check status code without payload checks.
- No skipping authorization tests.
- No skipping isolation tests in multi-context modules.
- No mixing Unit and Feature responsibilities.
- No relying solely on manual QA for critical modules.

---

# Output Requirements

When generating tests for a feature/module, provide:

1. Feature tests for all primary endpoints (success + 401 + 403 + 422 + 404)
2. Policy unit tests (permissions + scope + super-admin)
3. Unit tests for Handler/Action (business rules + orchestration)
4. Cache tests (if caching exists)
5. Event tests (if domain events exist)
6. Realtime tests (if realtime is enabled)
7. Versioning tests (if /v1 and /v2 exist)

All tests must be clean, readable, and maintainable.
Comments (if needed) must be in English.
Prefer explicit assertions and clear naming.

---

# Recommended Commands (Reference)

- Create feature test:
  php artisan make:test SomeEndpointTest

- Create unit test:
  php artisan make:test SomeHandlerTest --unit

- Run tests:
  php artisan test
  php artisan test --filter=SomeEndpointTest
