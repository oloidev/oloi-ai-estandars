---
name: laravel-multi-context-isolation-pattern
description: Enforces strict data isolation for multi-tenant and hierarchical scopes using Hexagonal Architecture (Ports & Adapters), with support for a global super-admin and sub-scope membership (e.g., Company -> Practice).
when_to_use:
  - implementing multi-tenant data isolation
  - building modules where data must not leak across customers
  - supporting hierarchical access (root scope + sub-scope)
  - adding caching to tenant-scoped queries
  - refactoring repositories/policies to enforce isolation
---

# Multi-Context Isolation Pattern (Hexagonal + Hierarchical Scopes)

You are implementing data isolation in a Laravel 12 Hexagonal Architecture project.

This pattern is generic across projects, but each project/module must explicitly define:
- Isolation Root (top-level boundary; e.g., RcmCompany, Company, Club)
- Optional Sub-Scope (nested boundary; e.g., Practice, Branch)
- Global Super-Admin override behavior (if applicable)

This project uses:
- Laravel 12
- PHP 8.4
- Octane (FrankenPHP)
- Sanctum
- Spatie Permission
- Redis (optional)
- Modular Monolith + Hexagonal Architecture

Isolation must be enforced in multiple layers: Repository, Policy, Cache, and Tests.

---

# Core Rule

Never rely on UI filtering, controllers, or middleware alone for isolation.

Isolation must be enforced at the data access boundary (Repository Adapter) and verified at the authorization boundary (Policy).

Queries must always be scoped by Isolation Root.
If a Sub-Scope exists, access must also be scoped by membership rules.

---

# Step 1 — Define the Isolation Model (Per Project or Module)

Before writing code, explicitly define:

1) Isolation Root entity and identifier:
- Example (MAI): RCMCompany (RcmCompanyId)
- Example (eTrucker): Company (CompanyId)
- Example (Arvox): Club (ClubId)

2) Optional Sub-Scope entity and identifier:
- Example (MAI): Practice (PracticeId)
- Example (Arvox): Branch (BranchId)

3) Membership rules:
- Can a user belong to multiple sub-scopes within the same root? (Yes/No)
- Can a user belong to multiple roots? (Yes/No)

4) Global Super-Admin override:
- Does a global super-admin exist? (Yes/No)

Write these rules down inside the module documentation or configuration notes.

---

# Step 2 — Create Value Objects for Scope IDs (Domain Layer)

In the Domain layer, create Value Objects for IDs:
- RootScopeId (project-specific naming preferred)
- SubScopeId (if applicable)

Examples:
- RcmCompanyId, PracticeId
- CompanyId
- ClubId, BranchId

Rules:
- Value Objects must be immutable.
- Must validate format/type.
- Must not depend on Laravel.

Location:
app/Modules/{ModuleName}/Domain/ValueObjects/

---

# Step 3 — Scope-Aware Ports (Repository Interfaces)

Repository Ports MUST require scope parameters explicitly.

Never allow repository methods that can return cross-scope data.

Bad:
- findById(EntityId $id)
- listAll()

Good (Root scoped):
- findById(EntityId $id, RootScopeId $rootId)
- listForRoot(RootScopeId $rootId, Filters $filters)

Good (Root + Sub-Scope scoped when applicable):
- findById(EntityId $id, RootScopeId $rootId, ?SubScopeId $subScopeId = null)
- listForSubScope(RootScopeId $rootId, SubScopeId $subScopeId, Filters $filters)

Rules:
- Root scope parameter is mandatory for all reads and writes.
- Sub-scope is mandatory when the use case is sub-scope restricted.
- Ports live in:
  app/Modules/{ModuleName}/Domain/Ports/Repositories/

---

# Step 4 — Enforce Isolation in Infrastructure Adapters (Eloquent Repositories)

Infrastructure repository implementations MUST enforce scope constraints at query time.

Rules:
- Always filter by Root scope.
- If Sub-scope is provided/required, filter by Sub-scope.
- If Sub-scope is derived via relationships, enforce with joins/whereHas.
- Never return unscoped results to Application.

Example patterns (generic):
- where('root_id', $rootId)
- where('sub_scope_id', $subScopeId)
- whereHas('subScope', fn($q) => $q->where('root_id', $rootId))

Location:
app/Modules/{ModuleName}/Infrastructure/Persistence/Eloquent/Repositories/

---

# Step 5 — Application Layer Must Pass Scope Explicitly

Handlers/Actions/Services in Application layer must always pass scope identifiers to repository ports.

Never infer scope inside repositories from auth() or request().

Rules:
- Scope must be carried in Commands/Queries DTOs.
- Scope must be derived once (e.g., from authenticated user context) at the boundary (Interfaces layer) and then passed explicitly.

Where to derive scope:
- Controller (Interfaces layer) maps authenticated user → RootScopeId (+ SubScopeIds)
- Then it builds a Command/Query object including scope

---

# Step 6 — Policy Enforcement (Spatie + Hierarchy)

Policies must enforce hierarchy BEFORE permission checks.

Standard order:

1) Global Super-Admin override
2) Root scope isolation match
3) Sub-scope membership (if applicable)
4) Spatie permission check

Rules:
- No inline role/permission checks in controllers.
- Policies must remain simple, deterministic, and testable.
- No heavy database logic inside Policy (prefer preloaded relations or lightweight checks).

Location:
app/Modules/{ModuleName}/Interfaces/Http/Policies/

---

# Step 7 — Cache Isolation (Redis) Must Namespace by Root (+ Sub-Scope)

Cache keys must ALWAYS include Root scope identifier.

If Sub-scope is relevant, include it too.

Key format guidelines:
- root:{RootId}:{module}:{operation}:{hash(filters)}
- root:{RootId}:sub:{SubId}:{module}:{operation}:{hash(filters)}

Rules:
- Never cache cross-scope data under shared keys.
- Cache must only be applied in Query Handlers (Application layer), using a Cache Port.
- Cache invalidation on write must be scoped to affected root/sub-scope keys or tags.

---

# Step 8 — Tests (Mandatory)

Must include tests that prove isolation cannot be bypassed:

1) Cross-root access denied
- Root A user cannot access Root B data.

2) Cross-sub-scope access denied (when sub-scope membership exists)
- User with Practice 1 cannot access Practice 2 within same root (if membership restricted).

3) Super-admin access allowed (if applicable)
- Super-admin can access across roots.

4) Cache isolation
- Cache key includes root (and sub-scope if needed).
- Cached response for Root A is not served to Root B.

Tests types:
- Policy tests
- Repository tests (scoping)
- Feature tests (HTTP 403)
- Query handler cache tests

---

# MAI Example (Reference Implementation)

MAI isolation model:
- Root scope: RCMCompany (rcm_companies)
- Sub-scope: Practice (practices)
- Super-admin: YES
- User belongs to multiple Practices within the same RCMCompany: YES
- User belongs to multiple RCMCompanies: NO

Rules:
- An RCMCompany user can access all practices under their company.
- A Practice user can access only the practices they are assigned to (within their company).
- No access across companies unless super-admin.

Repository port example:
- findClaimById(ClaimId $id, RcmCompanyId $companyId, ?PracticeId $practiceId = null)
- listClaims(RcmCompanyId $companyId, ?PracticeId $practiceId, Filters $filters)

Infrastructure query example (conceptual):
- Claims are scoped by company via practice relationship:
  - whereHas('practice', fn($q) => $q->where('rcm_company_id', $companyId))
- If practiceId is present, also filter by practice_id.

Policy example (conceptual order):
1) if user is super-admin → allow
2) if user.rcm_company_id !== practice.rcm_company_id → deny
3) if user is practice-scoped and not assigned to practiceId → deny
4) if user lacks permission claim.view → deny
5) allow

Cache keys:
- rcm-company:{companyId}:claims:{hash(filters)}
- rcm-company:{companyId}:practice:{practiceId}:claims:{hash(filters)}

---

# Prohibitions

- No unscoped repository methods that can return cross-root data.
- No reliance on controller-level filtering for isolation.
- No cache keys without root scope.
- No implicit scope inference from auth() inside repository implementations.
- No Gate::authorize inline in controllers.
- No role checks inside controllers.

---

# Output Requirements

When applying this pattern, produce:

1) Isolation model definition (Root + optional Sub-scope + membership rules + super-admin)
2) Scope Value Objects (Domain)
3) Repository Ports requiring scope
4) Infrastructure Adapters enforcing scope at query-time
5) Policy enforcing hierarchy
6) Cache key strategy (root + optional sub-scope)
7) Invalidation plan for writes
8) Tests proving cross-scope isolation and cache isolation

Always preserve existing functionality.
Prefer explicit scope passing over implicit inference.
Isolation must be impossible to bypass accidentally.