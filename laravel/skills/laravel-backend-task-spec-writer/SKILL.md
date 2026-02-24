---
name: laravel-backend-task-spec-writer
description: Generates a complete backend engineering task specification (ticket) for a feature/endpoint following OLOI Laravel 12 Hexagonal standards and selecting the correct supporting skills for implementation.
when_to_use:
  - turning a feature idea into an executable backend task
  - preparing work tickets for developers
  - defining scope, architecture, and deliverables before coding
  - ensuring implementation will follow hexagonal/CQRS/policy/tests/OpenAPI
---

# Backend Task Spec Writer (OLOI Laravel 12 + Hexagonal)

You are writing an engineering task specification for a Laravel 12 backend feature.

This project uses:
- Laravel 12 (API mode)
- PHP 8.4
- Octane (FrankenPHP)
- Sanctum
- Spatie Permission
- Modular Monolith + Hexagonal Architecture
- CQRS (pragmatic)
- Actions for complex writes
- Domain Events (optional)
- Redis caching (optional)
- ApiResponse standard
- OpenAPI annotations
- Dockerized environment

Your output is a TASK/TICKET that a developer can execute.
Do not write the full code. Write the plan, acceptance criteria, and deliverables.

---

# Core Rule

The task must be implementable using the existing skills:
- laravel-hexagonal-module-creation
- laravel-endpoint-creation
- laravel-policy-standard-spatie
- laravel-value-object-pattern
- laravel-cqrs-pattern
- laravel-action-pattern-enterprise
- laravel-domain-event-pattern
- laravel-multi-context-isolation-pattern
- laravel-redis-cache-pattern
- laravel-performance-octane-pattern
- laravel-observability-pattern
- laravel-api-versioning-pattern
- laravel-testing-standard
- laravel-realtime-event-integration-pattern (only if realtime enabled)

The task must explicitly list which skills are required and why.

---

# Step 1 — Ask for Required Inputs (Only if missing)

If not provided, request minimally:
- Module name (or confirm where it belongs)
- Entity name and main fields
- Isolation scope (root + sub-scope) if applicable
- Auth requirements (who can access?)
- API version (v1/v2)
- Any realtime requirement (enabled/disabled)
- Any caching requirement (yes/no)

If user already provided enough, proceed without follow-ups.

---

# Step 2 — Task Structure (Mandatory Sections)

Your task spec must include the following sections:

1) Title
2) Context
3) Goal
4) Scope (In / Out)
5) API Contract
   - Route(s)
   - Method
   - Request body
   - Response shape (ApiResponse)
6) Authorization & Isolation
   - Policy requirements
   - Spatie permission names
   - Isolation root / sub-scope rules
7) Architecture Plan (Hexagonal + CQRS)
   - Domain: Entity + Value Objects + Exceptions
   - Ports: repository ports (and cache port if needed)
   - Application: Commands/Queries + Handlers or Actions
   - Infrastructure: Eloquent adapters + cache adapters + listeners if needed
   - Interfaces: Controller + FormRequest + Resource
8) Caching Plan (Optional)
   - cache keys
   - TTL
   - invalidation strategy
9) Events Plan (Optional)
   - domain events emitted
   - listeners responsibilities
   - afterCommit requirement
   - realtime integration only if enabled
10) Observability
   - structured logs event names
   - correlation id usage expectation
   - duration_ms expectations for heavy ops
11) Tests (Mandatory)
   - Feature tests list
   - Unit tests list
   - Policy tests list
   - Isolation tests list
   - Cache/event tests if applicable
12) OpenAPI Documentation (Mandatory)
   - endpoints annotated
   - request/response schemas
13) Acceptance Criteria (Bullet list)
14) Definition of Done (Checklist)
15) Skill Selection Plan
   - list exact skills to be used in implementation
   - order of execution

No bold text is required unless the project standards demand it.

---

# Step 3 — Skill Selection Logic

Always include:
- laravel-endpoint-creation
- laravel-policy-standard-spatie
- laravel-testing-standard
- laravel-performance-octane-pattern
- laravel-observability-pattern

Include conditionally:
- laravel-cqrs-pattern (if any non-trivial read/write separation)
- laravel-action-pattern-enterprise (if transaction or multi-step write)
- laravel-value-object-pattern (if domain-specific fields exist)
- laravel-multi-context-isolation-pattern (if multi-tenant or hierarchical scopes exist)
- laravel-redis-cache-pattern (if heavy reads or dashboards)
- laravel-domain-event-pattern (if side effects exist)
- laravel-realtime-event-integration-pattern (only if realtime enabled)
- laravel-api-versioning-pattern (if v2+ or breaking changes)

---

# Step 4 — Output Requirements

The task spec must:
- Be written in clear, developer-ready language.
- Include exact file/folder targets per layer.
- Define exact permission names.
- Define test matrix (success + 401 + 403 + 422 + 404).
- Avoid implementation details that belong in code (no full code dumps).
- Preserve existing architecture and not remove unrelated functionality.

---

# Example Prompt Pattern (For User)

User: "Create a task to add an endpoint to store user injuries."

Your output must produce the complete ticket with:
- route: POST /api/v1/injuries
- data model assumptions (or ask for migration)
- policy + permissions
- CQRS command + action
- repository port
- tests
- OpenAPI
- observability

---

# Prohibitions

- Do not output full implementation code.
- Do not invent database fields if user says they already exist.
- Do not skip policies or tests.
- Do not suggest Gate::authorize usage.
- Do not place business logic in controllers.
- Do not ignore Octane safety constraints.

---

# Final Directive

Write tasks as if a senior tech lead is assigning work to a dev team.
Tasks must be complete, unambiguous, and enforce OLOI standards.

If any core inputs are missing, request them briefly, otherwise proceed.