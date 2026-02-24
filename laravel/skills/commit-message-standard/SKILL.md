---
name: commit-message-standard
description: Generates professional, industry-grade Git commit messages using Conventional Commits, including scope, context, breaking changes, and structured body aligned with OLOI architecture standards.
when_to_use:
  - after completing a backend feature
  - after refactoring architecture
  - after adding tests
  - after performance optimization
  - before opening a PR
  - when generating changelog-ready commits
---

# Commit Message Standard (Enterprise Grade)

All commits must follow:

Conventional Commits Specification

Format:

<type>(<scope>): <short summary>

<body>

<footer>

---

# Step 1 — Allowed Types

Use only:

feat        → new feature
fix         → bug fix
refactor    → internal improvement without behavior change
perf        → performance improvement
test        → tests added or modified
docs        → documentation only
chore       → non-functional changes (config, tooling)
build       → CI/CD or dependency changes
style       → formatting (no logic changes)

Breaking changes:

feat!: or refactor!: etc.

---

# Step 2 — Scope Naming Rules

Scope must reflect the module or architectural layer.

Examples:

injuries
claims
access-control
training-plans
auth
policy
repository
application
domain
cache
realtime
observability
versioning

If multi-module:

feat(injuries,policy)

Never use vague scopes like:
misc
update
stuff
changes

---

# Step 3 — Short Summary Rules

- Max ~72 characters
- Imperative mood
- No period at the end
- Clear and specific

Good:
feat(injuries): add v1 endpoint to store user injuries

Bad:
Added injuries endpoint
Fix stuff
Updates

---

# Step 4 — Body Structure (Mandatory for Features)

Body must include:

1) What was implemented
2) Architectural decisions
3) Isolation/caching/event impact
4) Tests added
5) Performance considerations (if applicable)

Structure:

- Implemented hexagonal module for injuries
- Added CQRS command + action for transactional write
- Integrated policy with Spatie permission injuries.create
- Enforced multi-context isolation (club + branch)
- Added Redis cache invalidation for injury queries
- Emitted InjuryCreated domain event (afterCommit)
- Added feature, unit, policy, and isolation tests
- Added OpenAPI annotations for v1 endpoint
- Ensured Octane-safe stateless services

Use bullet list format.

---

# Step 5 — Footer

Include references when available:

Refs: OLOI-123
Closes: OLOI-123
BREAKING CHANGE: explain if applicable

If no ticket system, omit Refs.

---

# Step 6 — When Multiple Layers Changed

If commit touches:

- Domain
- Application
- Infrastructure
- Interface
- Tests
- Docs

Mention explicitly in body.

---

# Step 7 — Performance & Octane Refactors

If performance-related:

perf(injuries): optimize query with eager loading and scoped cache

Body must include:
- Removed N+1
- Added Redis cache with TTL
- Ensured stateless service
- No static mutable state

---

# Step 8 — Refactors Without Behavior Change

refactor(injuries): extract injury write logic into Action

Body must include:
- No behavior change
- Tests remain green
- Improves separation of concerns

---

# Step 9 — Breaking Changes

If API contract changes:

feat(injuries)!: change injury response structure in v2

Footer must include:

BREAKING CHANGE:
- Removed field "severity_level"
- Added nested "severity" object

Never hide breaking changes.

---

# Step 10 — Output Requirements

When generating a commit message:

1) Produce the exact commit message ready to paste.
2) Include type + scope + summary.
3) Include structured bullet body.
4) Include footer if applicable.
5) Do not include explanations outside commit message.
6) Keep it professional and audit-ready.

---

# Example Output

feat(injuries): add v1 endpoint to store user injuries

- Implemented hexagonal injuries module
- Added StoreInjuryCommand and handler (CQRS)
- Introduced CreateInjuryAction with transactional safety
- Enforced policy injuries.create using Spatie Permission
- Applied multi-context isolation (club + branch scope)
- Emitted InjuryCreated domain event (afterCommit)
- Added Redis cache invalidation for injury list queries
- Implemented structured observability logs with request_id
- Added feature, unit, policy, and isolation tests
- Added OpenAPI documentation for v1 endpoint
- Ensured Octane-safe stateless services

Refs: OLOI-201

---

# Prohibitions

- No vague summaries.
- No single-line commits for major features.
- No mixing unrelated changes in one commit.
- No skipping mention of architecture decisions.
- No hiding breaking changes.
- No emojis in backend production commits (unless team standard allows).

---

# Final Directive

Commit messages must be:

- Explicit
- Structured
- Auditable
- Changelog-ready
- Architecture-aware
- Safe for enterprise environments