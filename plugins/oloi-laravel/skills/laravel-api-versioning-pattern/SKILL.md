---
name: laravel-api-versioning-pattern
description: Implements structured API versioning in a Laravel 12 Hexagonal Modular Monolith, ensuring backward compatibility, safe evolution, and multi-client support.
when_to_use:
  - introducing breaking API changes
  - supporting mobile + web + wearables simultaneously
  - evolving response contracts
  - preparing long-term platform stability
---

# API Versioning Pattern (Hexagonal + Production Safe)

You are implementing API versioning in a Laravel 12 API running in a Modular Monolith with Hexagonal Architecture.

This project may support:
- Web clients
- Mobile apps
- Wearables
- TV dashboards
- Third-party integrations

API versioning must preserve backward compatibility and avoid breaking existing clients.

---

# Core Principle

Breaking changes must never affect existing clients.

Versioning must be:
- Explicit
- Route-based (preferred)
- Documented
- Isolated at controller/interface layer
- Backward compatible

---

# Step 1 — Versioning Strategy

Preferred strategy: Route Prefix Versioning

Example:

/api/v1/...
/api/v2/...

Do not use:
- Implicit versioning
- Version inside controller logic
- Conditional response switching based on headers

Keep versioning explicit and clear.

---

# Step 2 — Route Structure

In routes/api.php:

Group routes by version:

Route::prefix('v1')->group(function () {
    // v1 routes
});

Route::prefix('v2')->group(function () {
    // v2 routes
});

Middleware (auth, etc.) must be applied per version group.

---

# Step 3 — Controller Separation

Controllers must be versioned at the Interfaces layer.

Example structure:

app/Modules/{ModuleName}/Interfaces/Http/V1/Controllers/
app/Modules/{ModuleName}/Interfaces/Http/V2/Controllers/

Rules:
- Do not mix V1 and V2 logic in the same controller.
- V2 must not modify V1 behavior.
- Shared business logic remains in Application layer.

Only the Interface layer changes between versions.

---

# Step 4 — Resource Versioning

Response Resources must be versioned when structure changes.

Example:

app/Modules/{ModuleName}/Interfaces/Http/V1/Resources/
app/Modules/{ModuleName}/Interfaces/Http/V2/Resources/

If response contract changes:
- Create new Resource for new version.
- Do not modify old Resource.

Never silently change response fields in existing version.

---

# Step 5 — Backward Compatibility Rules

Allowed in minor version update:
- Add optional fields
- Add new endpoints
- Improve performance

Breaking changes require:
- New version prefix (v2, v3, etc.)
- Separate controllers/resources
- Updated OpenAPI docs

Never:
- Remove fields from an existing version.
- Change data types.
- Rename fields in same version.

---

# Step 6 — OpenAPI Documentation

OpenAPI docs must be version-aware.

Each version must:
- Define its own paths.
- Have accurate schemas.
- Reflect exact response structure.

Documentation must clearly state version.

---

# Step 7 — Deprecation Strategy

When introducing v2:

- Keep v1 active.
- Mark v1 endpoints as deprecated in documentation.
- Optionally log usage of deprecated endpoints.

Do not immediately remove old versions.

Removal process:
1. Announce deprecation.
2. Monitor usage.
3. Remove in major release only.

---

# Step 8 — Domain & Application Isolation

Versioning must NOT affect:

- Domain Entities
- Value Objects
- Repository Ports
- Business Rules
- Isolation Pattern
- Policies

Versioning only affects:
- HTTP layer
- Request validation differences
- Response formatting
- Optional orchestration changes

Business logic remains version-agnostic.

---

# Step 9 — Realtime & Versioning

If realtime is enabled:

- Channel naming may include version only if contract differs.
- Prefer keeping internal events version-agnostic.
- Transform payload at interface layer if needed.

Do not version Domain Events unnecessarily.

---

# Step 10 — Testing Requirements

For each version:

- Feature tests for v1.
- Feature tests for v2.
- Ensure v1 behavior remains unchanged after v2 introduction.
- Validate OpenAPI matches actual responses.

Never assume compatibility without tests.

---

# Prohibitions

- No conditional version logic inside same controller.
- No modifying v1 resources to support v2.
- No mixing versioned and non-versioned routes.
- No breaking response contract silently.
- No removing endpoints without deprecation cycle.

---

# Output Requirements

When implementing API versioning:

1) Provide versioned route group.
2) Provide versioned controller(s).
3) Provide versioned resource(s).
4) Ensure shared Application layer is reused.
5) Provide OpenAPI annotations per version.
6) Provide tests validating both versions.
7) Preserve existing functionality.

Versioning must be explicit, isolated, and predictable.
Never compromise stability for convenience.