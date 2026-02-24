# OLOI Laravel 12 Backend AI Standards

## Project Context

This backend is built using:

- Laravel 12 (API mode)
- PHP 8.4
- Laravel Octane (FrankenPHP)
- Sanctum authentication
- Spatie Permission (roles & permissions)
- Dockerized environment
- MySQL / MariaDB
- Redis when required
- OpenAPI annotations for documentation
- Standardized ApiResponse helper

This is a high-performance, production-grade backend.
All generated code must follow enterprise standards.

---

# Core Architectural Principles

1. Clean Architecture
2. SOLID principles
3. Modular Monolith structure
4. Domain-oriented organization
5. Strict separation of concerns
6. Explicit typing (PHP 8.4 strict typing)
7. Test-first mindset
8. Performance awareness (Octane-safe code)

Controllers must remain thin.
Business logic must never live inside controllers.

---

# Modular Structure Standard

All new features must be structured as domain modules:

app/Modules/{ModuleName}/
    Models/
    Policies/
    Repositories/
        Contracts/
        Eloquent/
    Services/
    Actions/
    DTOs/
    Resources/
    Requests/
    Exceptions/
    Tests/

Controllers must be placed in:

app/Http/Controllers/Api/{ModuleName}/

Modules must be self-contained and scalable.

---

# API Response Standard

All responses must use the ApiResponse helper.

Structure:

{
    success: boolean,
    message: string,
    data: object|null,
    errors: array|null
}

No raw JSON responses.
No direct return response()->json().

---

# Authorization Standard

- All authorization must use Policies.
- Policies must integrate with Spatie Permission.
- Never use Gate::authorize directly.
- Never perform inline role checks in controllers.
- Authorization must be explicit and testable.

---

# Validation Standard

- All validation must use FormRequest classes.
- No inline validation inside controllers.
- FormRequest must define:
  - rules()
  - authorize()
  - custom messages (when needed)

---

# Repository Pattern Standard

Repositories must:

- Be interface-driven (Contracts folder).
- Handle only data access.
- Avoid business logic.
- Always eager load required relationships.
- Prevent N+1 queries.
- Be Octane-safe (no static mutable state).

---

# Service Layer Standard

Services must:

- Contain business logic.
- Be stateless.
- Use repositories via interfaces.
- Be independently testable.
- Never call DB directly.
- Handle orchestration between repositories.

---

# Actions Pattern Standard

Use Actions when:

- A business operation is complex.
- A transaction is required.
- It represents a specific use case.
- The logic must be reusable.

Actions must:

- Be atomic.
- Handle transactions safely.
- Be injectable.
- Return structured results.

---

# Octane Safety Rules

Because this project uses Laravel Octane:

- No static mutable properties.
- No per-request container state leakage.
- No singleton data mutation.
- Avoid global state.
- Avoid request-specific caching inside singletons.
- Ensure services are stateless.

All code must be Octane-compatible.

---

# Database Standards

- All foreign keys must be indexed.
- SoftDeletes when business logic requires it.
- Avoid nullable critical business columns.
- Use proper casting.
- Use enums when appropriate.
- Avoid hidden side effects in model events.
- Prefer explicit transactions inside Actions.

---

# Testing Standards

Every new module must include:

- Feature tests
- Unit tests for Services
- Policy tests
- Validation tests
- Action tests (if applicable)

Tests must cover:
- Authorization
- Validation
- Happy path
- Failure cases

---

# OpenAPI Documentation Standard

All endpoints must include:

- @OA\Get
- @OA\Post
- @OA\Put
- @OA\Patch
- @OA\Delete

Each endpoint must define:

- Summary
- Description
- Parameters
- Request body schema
- Response schemas
- Security

Documentation must be in English.

---

# Code Quality Standards

- Strict types enabled.
- Typed properties required.
- Explicit return types required.
- No unused imports.
- No dead code.
- No hidden magic.
- Clear naming conventions.
- PSR-12 compliance.

---

# Skill Selection Strategy

When user requests:

- "Create endpoint"
  → Load endpoint-creation skill.
- "Create module"
  → Load ddd-module-creation skill.
- "Add authorization"
  → Load policy-standard skill.
- "Optimize performance"
  → Load performance-octane skill.
- "Create business logic"
  → Load action-pattern skill.
- "Write tests"
  → Load testing-standard skill.
- "Add documentation"
  → Load openapi-standard skill.

Agent must analyze the request before selecting skills.
Multiple skills may be combined when appropriate.

---

# Strict Prohibitions

- No business logic inside controllers.
- No inline validation.
- No Gate::authorize usage.
- No direct Eloquent calls inside controllers.
- No N+1 queries.
- No raw JSON responses.
- No skipping tests.
- No ignoring Octane safety.

---

# Developer Safety Rules

Agent must:

- Analyze existing code before modifying it.
- Never remove unrelated functionality.
- Preserve existing architecture.
- Respect modular boundaries.
- Generate scalable and maintainable code.
- Always prioritize clarity over cleverness.

---

# Final Directive

This backend follows enterprise-level architecture.
All generated code must be:

- Clean
- Scalable
- Testable
- Secure
- Octane-safe
- Fully documented

If unsure, prefer explicit architecture over shortcuts.