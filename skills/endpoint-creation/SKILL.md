---
name: endpoint-creation
description: Creates a new HTTP endpoint in a Laravel 12 Hexagonal module following OLOI enterprise standards.
when_to_use:
  - creating a new API endpoint
  - adding a new route to a module
  - exposing a new use case through HTTP
  - implementing CRUD operations
---

# Endpoint Creation Standard (Hexagonal + Laravel 12)

You are creating a new HTTP endpoint inside a Hexagonal Architecture module.

This project uses:

- Laravel 12 (API mode)
- PHP 8.4
- Octane (FrankenPHP)
- Sanctum
- Spatie Permission
- Redis (optional)
- ApiResponse helper
- OpenAPI annotations

All endpoints must respect modular hexagonal boundaries.

---

# Step 1 — Identify Module

Confirm the module exists under:

app/Modules/{ModuleName}/

If not, suggest using the "hexagonal-module-creation" skill first.

Never create endpoints outside module boundaries.

---

# Step 2 — Define Use Case First (Application Layer)

Before creating the controller:

1. Identify whether this is:
   - Command (write operation)
   - Query (read operation)

2. Create corresponding:

Application/UseCases/{OperationName}Command or Query
Application/Handlers/{OperationName}Handler

Controller must call Handler.
Never place business logic in controller.

---

# Step 3 — Create FormRequest

Under:

Interfaces/Http/Requests/

Create:

{OperationName}Request

Rules:

- Define validation rules.
- Define authorize() method.
- No validation inside controller.
- Map validated input to DTO or Command object.

---

# Step 4 — Create Controller (Thin Only)

Under:

Interfaces/Http/Controllers/

Controller must:

- Inject Handler or Action.
- Call use case.
- Wrap response using ApiResponse::success() or ApiResponse::error().
- Use Policy for authorization.
- Not contain business logic.
- Not call Eloquent directly.

Example flow:

Controller
  → FormRequest
  → Handler
  → Domain
  → Repository Port
  → Infrastructure Adapter

---

# Step 5 — Authorization

- Use Laravel Policy.
- Policy must integrate with Spatie roles.
- Never use Gate::authorize inline.
- Controller must call $this->authorize().

Policies must be testable.

---

# Step 6 — Resource Formatting

If returning structured output:

Create:

Interfaces/Http/Resources/{ModuleName}Resource

Resource formats data.
ApiResponse wraps it.

Never return raw models.

---

# Step 7 — Route Definition

Define route in:

routes/api.php

Follow RESTful conventions:

- GET → Query
- POST → Create
- PUT/PATCH → Update
- DELETE → Delete

Apply middleware:

- auth:sanctum
- permission (if required)

---

# Step 8 — OpenAPI Documentation

Every endpoint must include:

- @OA annotation
- Summary
- Description
- Parameters
- Request body schema
- Response schemas
- Security

Documentation must be in English.

---

# Step 9 — Optional Caching (Read Endpoints Only)

If endpoint is heavy or frequently requested:

- Ensure it is a Query.
- Use Cache Port (not direct Redis).
- Apply caching inside Application layer.
- Ensure proper cache invalidation on write operations.
- Never cache inside Controller.
- Never use static variables for caching (Octane risk).

---

# Step 10 — Octane Safety

Ensure:

- No static mutable properties.
- No storing request/user in class properties.
- No global state.
- Handlers and Services must be stateless.

---

# Step 11 — Testing Requirements

Create:

- Feature test for endpoint.
- Validation test.
- Policy authorization test.
- Handler unit test.
- Cache behavior test (if applicable).

Tests must cover:

- Success case
- Unauthorized case
- Validation failure
- Edge cases

---

# Error Handling

- Domain exceptions must be mapped to proper HTTP responses.
- Do not expose internal errors.
- Use ApiResponse::error() consistently.

---

# Prohibitions

- No business logic inside Controller.
- No Eloquent calls inside Controller.
- No inline validation.
- No skipping Policy.
- No raw JSON responses.
- No bypassing Application layer.
- No ignoring Octane safety.

---

# Output Requirements

When generating an endpoint:

1. Provide route definition.
2. Provide FormRequest.
3. Provide Policy (if needed).
4. Provide Use Case / Command / Query.
5. Provide Handler.
6. Provide Controller.
7. Provide Resource (if needed).
8. Provide OpenAPI annotations.
9. Provide tests.
10. Ensure code is clean and typed.

Always analyze existing module before generating new code.
Never remove unrelated functionality.
Follow Hexagonal boundaries strictly.