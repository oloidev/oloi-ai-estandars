---
name: laravel-testing-standard
description: Creates complete Laravel 12 test coverage (Feature + Unit) and enforces safe Docker-first test execution with explicit test-environment validation.
when_to_use:
  - creating tests for a new endpoint/module
  - adding missing coverage to an existing feature
  - verifying authorization, validation, isolation, and caching
  - preventing regressions during refactors (hexagonal/CQRS)
  - ensuring Octane-safe behavior via deterministic tests
  - running tests in Dockerized Laravel environments safely
---

# Testing Standard (Laravel 12 + Octane + Docker Safe Mode)

You are implementing and running tests in a Laravel API where runtime is Dockerized.

This project may use:
- PHP 8.4
- Octane (FrankenPHP)
- Sanctum
- Spatie Permission
- Modular Monolith / Hexagonal (when applicable)
- CQRS / Actions
- Domain Events
- Redis (optional)
- ApiResponse helper

Tests must be reliable, deterministic, and safe for local/dev data.

---

# Core Rule (Non-Negotiable)

Always run tests inside the PHP/backend container.
Never run project tests directly on host OS when the project is Dockerized.

Before running tests, you must validate:
1. Container resolution (which container runs PHP artisan)
2. Testing DB isolation (test DB must be different from local/dev DB)
3. Laravel config cache impact (cached config must not force prod/dev DB)

If any of these checks fail, stop and fix the setup first.

---

# Step 0 — Preflight: Locate Runtime Container

Resolve backend container in this order:

1. If user provides container name, use it.
2. Else, discover via Docker Compose:
   - `docker compose ps --services --status running`
   - pick likely service names: `app`, `php`, `backend`, `api`
3. Resolve concrete container:
   - `docker compose ps -q <service>` then inspect name
4. Fallback:
   - `docker ps` and choose container with PHP/Laravel image or known project labels.

Record selected container as `PHP_CONTAINER`.

---

# Step 1 — Preflight: Validate Test Environment Safety

Inside `PHP_CONTAINER`, check:

1. PHP available:
   - `php -v`

2. Test config files exist:
   - `phpunit.xml`
   - `.env.testing` (if used by project)

3. DB isolation:
   - Compare runtime DB (from `.env` or cached config) vs test DB (`phpunit.xml` / `.env.testing`).
   - Test DB must be separate (e.g. `wodlogi_test`), never main DB (`wodlogi_db`).

4. Config cache risk:
   - If `bootstrap/cache/config.php` exists, cached DB settings may override env.
   - Ensure tests bypass cache with dedicated temp cache paths via env:
     - `APP_CONFIG_CACHE=/tmp/...`
     - `APP_EVENTS_CACHE=/tmp/...`
     - `APP_ROUTES_CACHE=/tmp/...`
     - `APP_PACKAGES_CACHE=/tmp/...`
     - `APP_SERVICES_CACHE=/tmp/...`

5. Driver compatibility:
   - If migrations contain MySQL-only SQL (`ALTER ... MODIFY ... ENUM`), do not use sqlite memory for full suite.
   - Use isolated MySQL test DB.

If unsafe, fix config before writing/running tests.

---

# Step 2 — Test Execution Policy (Docker-Only)

Run tests inside container:
- `docker exec <PHP_CONTAINER> sh -lc 'cd /var/www/html && php artisan test'`
- Filtered:
- `docker exec <PHP_CONTAINER> sh -lc 'cd /var/www/html && php artisan test --filter=SomeTest'`

When config cache isolation is needed, prepend env vars in same command:
- `APP_CONFIG_CACHE=/tmp/... APP_EVENTS_CACHE=/tmp/... ... php artisan test ...`

Never run `php artisan test` on host for dockerized project.

---

# Step 3 — Required Test Coverage

## A) Feature Tests (HTTP)
Must cover:
- success (200/201)
- 401 unauthenticated
- 403 unauthorized
- 422 validation
- 404 scoped not found
- list filters/pagination where applicable

Assert:
- ApiResponse shape (`status`, `message`, `data` / `errors`)
- resource contract fields
- no sensitive leakage

## B) Unit Tests (Application)
Must cover:
- Handler/Action orchestration
- business rules
- port mocking
- deterministic outcomes

## C) Policy Tests
Must cover:
- allowed with permission/scope
- denied without permission
- cross-scope/root denial
- super-admin path (if applicable)

## D) Request Validation Tests
Must cover:
- required/type/boundary rules
- 422 payload behavior

## E) Repository/Integration Tests (when needed)
Must cover:
- persistence mapping
- scoping constraints
- key query behavior

---

# Multi-Context Isolation Requirements

If module is context-aware, include:
1. Cross-root denial
2. Cross-sub-scope denial
3. Positive case with correct scope permissions

---

# Cache / Events / Realtime (Conditional)

Add tests only when feature uses them:
- Cache hit/miss + invalidation
- Domain events emitted after commit
- Realtime listeners behavior (if enabled)

---

# Prohibitions

- Do not run dockerized project tests on host.
- Do not run tests when test DB points to main/local DB.
- Do not trust cached config blindly.
- Do not skip auth/isolation tests.
- Do not create brittle time/order-dependent tests.

---

# Output Requirements

When delivering testing work:
1. List preflight checks performed (container + DB safety + cache safety).
2. Show exact Docker command used to run tests.
3. Provide created/updated test files.
4. Summarize pass/fail and failing reason if any.
5. If blocked, state exact setup gap and safest remediation.

---

# Recommended Commands (Docker)

- Discover containers:
  - `docker compose ps`
  - `docker ps`

- Run one test safely:
  - `docker exec <PHP_CONTAINER> sh -lc 'cd /var/www/html && php artisan test --filter=SomeEndpointTest --stop-on-failure'`

- Run suite safely:
  - `docker exec <PHP_CONTAINER> sh -lc 'cd /var/www/html && php artisan test'`
