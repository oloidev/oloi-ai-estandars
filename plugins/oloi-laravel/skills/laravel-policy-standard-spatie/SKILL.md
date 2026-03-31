---
name: laravel-policy-standard-spatie
description: Enforces a strict authorization pattern using Laravel Policies integrated with Spatie Permission in a Hexagonal Modular Monolith architecture.
when_to_use:
  - creating a new endpoint
  - adding authorization to a module
  - implementing role or permission checks
  - securing multi-tenant operations
  - defining ownership rules
---

# Policy Standard (Spatie + Hexagonal + Multi-Tenant Safe)

You are implementing authorization in a Laravel 12 Hexagonal Architecture project using Spatie Permission.

This project uses:

- Laravel 12
- PHP 8.4
- Sanctum
- Spatie Permission
- Modular Monolith
- Hexagonal Architecture
- Octane (FrankenPHP)

Authorization must be explicit, testable, multi-tenant aware, and policy-driven.

---

# Core Rule

All authorization must go through Laravel Policies.

Never:

- Use Gate::authorize directly in controllers.
- Use hasRole() in controllers.
- Use hasPermissionTo() in controllers.
- Hardcode role checks inside business logic.
- Rely only on route middleware for complex security.

Controller must call:

$this->authorize('action', $model);

---

# Step 1 — Policy Location

Policies must live inside the module:

app/Modules/{ModuleName}/Interfaces/Http/Policies/

If using global registration, register policy in:

Infrastructure/Providers/{ModuleName}AuthServiceProvider

Do not scatter policies across unrelated folders.

---

# Step 2 — Policy Structure

Policy methods must:

- Receive User and optionally Model.
- Use Spatie permissions.
- Enforce tenant isolation if applicable.
- Enforce ownership rules if applicable.
- Return boolean only.
- Remain simple and readable.

Example method structure:

1. Check permission via Spatie.
2. Check tenant_id match (if multi-tenant).
3. Check ownership (if applicable).
4. Return true or false.

Never perform database-heavy logic inside policy.

---

# Step 3 — Permission Naming Convention

Permissions must follow this pattern:

module.action

Examples:

claim.create
claim.update
claim.delete
training.view
training.manage

Never use inconsistent naming.
Never embed role names inside permission names.

Roles are groups.
Permissions are capabilities.

---

# Step 4 — Multi-Tenant Enforcement

If the module is multi-tenant:

Policy must verify:

$user->tenant_id === $model->tenant_id

or equivalent domain rule.

Never assume middleware handles isolation.
Isolation must be explicit in policy.

If operation is global (e.g. super-admin):

Explicitly check permission for that case.

---

# Step 5 — Ownership Rule

If entity has ownership:

Example:

$user->id === $model->user_id

Ownership check must be separate from permission check.

Do not mix business logic with authorization.

Authorization verifies:
- Capability
- Isolation
- Ownership

It does not modify state.

---

# Step 6 — Controller Requirements

Controller must:

- Inject model via route model binding.
- Call $this->authorize().
- Not check roles manually.
- Not use hasRole().
- Not use hasPermissionTo() directly.

Authorization must happen before calling application use case.

---

# Step 7 — Application Layer Isolation

Application and Domain layers must not depend on Spatie.

Policies belong to Interfaces layer.

Domain logic must not call permission checks directly.

Authorization is a delivery concern, not a domain concern.

---

# Step 8 — Route Middleware

Route middleware allowed:

- auth:sanctum

Permission middleware is allowed only for simple gates,
but must not replace Policy for model-specific operations.

Never rely only on middleware for complex authorization.

---

# Step 9 — Testing Requirements

Each module must include:

- Policy unit test.
- Authorized scenario test.
- Unauthorized scenario test.
- Cross-tenant access test.
- Ownership violation test (if applicable).

Feature tests must verify:

- 403 response when unauthorized.
- Correct behavior when authorized.

---

# Step 10 — Octane Safety

Policies must:

- Not store state in properties.
- Not cache results in static variables.
- Not depend on mutable singletons.
- Remain stateless.

All checks must be deterministic per request.

---

# Step 11 — Advanced Rule (Optional)

For complex permission logic:

Extract rule into:

Application/Services/AuthorizationService

Policy calls that service.

Never embed complex branching logic inside controller.

---

# Security Prohibitions

- No hardcoded role strings in controllers.
- No inline permission checks.
- No skipping tenant validation.
- No skipping ownership validation.
- No Gate::authorize outside policy.
- No security logic inside Domain entities.

---

# Output Requirements

When implementing authorization:

1. Provide Policy class.
2. Provide permission naming.
3. Provide example role-permission assignment.
4. Provide Controller usage example.
5. Provide Policy tests.
6. Ensure multi-tenant awareness if applicable.
7. Ensure ownership validation if applicable.

Never remove unrelated functionality.
Always preserve architectural boundaries.
Authorization must be explicit, layered, and testable.