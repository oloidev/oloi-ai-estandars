---
name: hexagonal-module-creation
description: Creates a new Laravel module using Hexagonal Architecture (Ports & Adapters) inside a Modular Monolith structure.
when_to_use:
  - creating a new business module
  - refactoring legacy module to hexagonal
  - designing a complex domain feature
  - implementing a new bounded context
---

# Hexagonal Module Creation Standard

You are creating a new Laravel 12 module using Hexagonal Architecture (Ports & Adapters) inside a Modular Monolith.

This project uses:

- Laravel 12 (API mode)
- PHP 8.4
- Octane (FrankenPHP)
- Sanctum
- Spatie Permission
- Redis (optional)
- ApiResponse helper

You must strictly follow these steps.

---

# Step 1 — Create Module Structure

Create the module under:

app/Modules/{ModuleName}/

With this structure:

app/Modules/{ModuleName}/
    Domain/
        Entities/
        ValueObjects/
        Enums/
        Exceptions/
        Ports/
            Repositories/
            Cache/
            Services/
    Application/
        DTOs/
        UseCases/
            Commands/
            Queries/
        Handlers/
        Services/
        Actions/
    Infrastructure/
        Persistence/
            Eloquent/
                Models/
                Repositories/
        Cache/
            Redis/
        Providers/
    Interfaces/
        Http/
            Controllers/
            Requests/
            Resources/
        Console/
        Jobs/
    Tests/

Do not skip any main layer.

---

# Step 2 — Define Domain First

Always start from Domain.

Create:

- Entity representing the business concept.
- Value Objects for business-specific concepts.
- Enums for states.
- Domain Exceptions.
- Repository Port (interface).

Rules:

- Domain must not depend on Laravel.
- No Eloquent.
- No Facades.
- No Request.
- No Container.
- No Redis.
- No HTTP logic.

Domain must be pure PHP.

---

# Step 3 — Define Repository Port

Inside:

Domain/Ports/Repositories/

Create an interface like:

{ModuleName}Repository

It must define:

- findById()
- save()
- delete() (if applicable)
- query methods required by domain

No implementation here.

---

# Step 4 — Create Application Layer (Use Cases)

Create use cases under:

Application/UseCases/

For write operations:
- Create{ModuleName}Command
- Update{ModuleName}Command
- Delete{ModuleName}Command

For read operations (if needed):
- Get{ModuleName}Query
- List{ModuleName}Query

Then create corresponding Handlers.

Handlers must:

- Receive DTO/Command
- Use repository port
- Orchestrate domain logic
- Be stateless
- Not call Eloquent directly

If business operation is complex, wrap logic in an Action class.

---

# Step 5 — Infrastructure Layer (Adapters)

Inside:

Infrastructure/Persistence/Eloquent/

Create:

- Eloquent Model
- Eloquent Repository implementing Domain Repository Port

Mapping rules:

- Convert Eloquent Model → Domain Entity
- Convert Domain Entity → Eloquent Model

Never expose Eloquent Models to Application layer.

If caching is required:

- Define Cache Port in Domain.
- Implement Redis adapter under Infrastructure/Cache/Redis/.

---

# Step 6 — Interfaces Layer (HTTP)

Inside:

Interfaces/Http/

Create:

- Controller
- FormRequest
- Resource

Controller must:

- Be thin.
- Call Application Handler.
- Return ApiResponse::success() or ApiResponse::error().
- Use Policy for authorization.

No business logic allowed.

---

# Step 7 — Policy Integration

Create Policy under Interfaces layer or Laravel default policy location.

Policy must:

- Integrate with Spatie roles.
- Never use Gate::authorize inline.
- Be testable.

---

# Step 8 — OpenAPI Documentation

All endpoints must include:

- @OA\Get / @OA\Post / etc.
- Summary
- Description
- Parameters
- Request schema
- Response schema
- Security definition

Documentation must be in English.

---

# Step 9 — Octane Safety

Ensure:

- No static mutable properties.
- No state stored between requests.
- No singletons storing request data.
- Services and handlers must be stateless.

---

# Step 10 — Testing

Create:

- Unit tests for Domain.
- Unit tests for Application handlers.
- Feature tests for HTTP endpoints.
- Policy tests.
- Cache behavior tests (if applicable).

---

# Optional — CQRS

If domain is complex:

- Separate Commands and Queries.
- Queries may use optimized read repositories.
- Cache may be applied only to Queries.

Do not introduce CQRS for simple CRUD.

---

# Prohibitions

- No Eloquent inside Domain.
- No Facades inside Domain.
- No business logic in Controllers.
- No inline validation.
- No skipping layers.
- No hidden N+1 queries.
- No raw JSON responses.

---

# Output Requirement

When generating a module:

1. Provide folder structure.
2. Provide Domain Entity.
3. Provide Repository Port.
4. Provide Use Case + Handler.
5. Provide Eloquent Adapter.
6. Provide Controller.
7. Provide FormRequest.
8. Provide Policy.
9. Provide basic tests.
10. Provide OpenAPI annotation.

Code must be clean, typed, and production-ready.

If unsure, prioritize architectural clarity over brevity.