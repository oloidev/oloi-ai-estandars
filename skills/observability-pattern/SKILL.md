---
name: observability-pattern
description: Adds enterprise-grade observability (structured logging, correlation IDs, tenant/scope-aware context, metrics, and safe error reporting) for Laravel 12 Octane + Hexagonal Modular Monolith.
when_to_use:
  - diagnosing production issues (Octane, Redis, realtime, heavy endpoints)
  - adding structured logs to new modules/endpoints
  - implementing correlation/tracing across services and jobs
  - measuring performance of critical queries and actions
  - improving error reporting without leaking sensitive data
---

# Observability Pattern (Laravel 12 + Octane + Hexagonal)

You are implementing observability in a Laravel 12 API running on Octane (FrankenPHP).

This project uses:
- Laravel 12
- PHP 8.4
- Octane (FrankenPHP)
- Modular Monolith + Hexagonal Architecture
- Redis (optional)
- Jobs/Queues (optional)
- ApiResponse helper

Observability must be safe (no sensitive leakage), scope-aware (multi-context isolation),
and consistent across HTTP, Jobs, and Event listeners.

---

# Core Goals

1) Trace a request end-to-end (correlation ID)
2) Understand "what happened" (structured logs)
3) Measure "how long it took" (timings/metrics)
4) Debug safely (no secrets/PHI/PII in logs)
5) Link failures to scope (root + sub-scope when applicable)

---

# Step 1 — Correlation ID (Request ID)

Each incoming request must have a correlation ID:

- If header `X-Request-Id` exists, reuse it.
- Otherwise generate a UUID.

The correlation ID must be:
- Included in all logs for that request
- Included in ApiResponse metadata (optional if your ApiResponse supports it)
- Propagated to queued jobs and event handling

Never store correlation IDs in static mutable variables (Octane risk).

---

# Step 2 — Scope-Aware Context (Isolation Root + Sub-Scope)

If the project/module is multi-context isolated:
- Add isolation context to logs:
  - root_scope_id (e.g., rcm_company_id / company_id / club_id)
  - sub_scope_ids when relevant (e.g., practice_ids / branch_id)

Rules:
- Do NOT infer scope in repositories from auth() (architecture rule),
  but for logging you may attach scope derived at boundary.

Never log raw tokens or sensitive identifiers beyond what is needed.

---

# Step 3 — Structured Logging Only

All logs must be structured (context arrays), not concatenated strings.

Bad:
- Log::info("User {$id} failed ...")

Good:
- Log::info("claim.update_failed", [
    'request_id' => $requestId,
    'root_scope_id' => $rootId,
    'claim_id' => $claimId,
    'reason' => 'validation_failed',
  ]);

Rules:
- Use event-like names: module.action.outcome
- Always include request_id
- Include scope when applicable
- Include durations when applicable

---

# Step 4 — Log Levels & What to Log

- debug: development-only, do not spam in production
- info: normal business events (created/updated/submitted)
- warning: recoverable issues (retryable external API, soft failures)
- error: failures needing attention (exceptions, failed transactions)

Do not log:
- passwords
- tokens
- full request bodies containing sensitive info
- PHI/PII unless strictly required and approved (avoid by default)

---

# Step 5 — Timing and Performance Metrics

Measure durations for:
- Controllers (request duration)
- Query Handlers (read performance)
- Actions (write performance)
- External API calls
- Cache hits/misses (at least counters in logs)

Minimal requirement:
- Log duration_ms for critical operations.

Example:
- "dashboard.query_completed" with duration_ms and cache_hit boolean.

Avoid expensive timing instrumentation in tight loops.

---

# Step 6 — Exception Handling Standard

All exceptions must be:
- mapped to ApiResponse::error()
- logged with request_id and scope context
- sanitized (no stack traces in response; stack traces allowed in logs depending on env)

Rules:
- Domain Exceptions should log as warning or info depending on severity.
- Infrastructure Exceptions should log as error.

Never expose internal exception messages to clients unless explicitly safe.

---

# Step 7 — Jobs and Event Listeners Propagation

When dispatching Jobs:
- Include request_id in the payload/context if originating from HTTP.
- Include root_scope_id and relevant IDs if needed.

When handling Domain Events:
- Log event handling start/end with request_id if available.
- Ensure idempotency failures are logged with event_id.

---

# Step 8 — Realtime Observability (If Enabled)

If realtime is enabled:
- Log broadcast attempts with:
  - request_id (or event_id)
  - channel (scoped)
  - event_name
  - payload_size (approx)
  - duration_ms
  - outcome (success/fail)

Never log full socket payloads if they may include sensitive data.
Prefer IDs and summary fields.

---

# Step 9 — Audit-Safe Business Logs (Optional)

For high-stakes modules (e.g., billing/claims):
- Emit "audit events" as info logs with:
  - actor_user_id (or system)
  - action
  - entity_id
  - scope ids
  - timestamp
  - result

Do not replace a proper audit table if required, but logs provide traceability.

---

# Step 10 — Testing Requirements

Tests should validate:
- request_id is present in responses/log context (where feasible)
- scope context is attached when multi-context isolation is enabled
- errors are sanitized in response
- key operations log duration_ms
- jobs/listeners propagate correlation id (unit/integration as applicable)

---

# Prohibitions

- No logging secrets/tokens/passwords
- No raw dumps of full request bodies
- No concatenated string logs for structured events
- No static/global correlation id storage (Octane risk)
- No noisy logging in hot paths without purpose

---

# Output Requirements

When implementing observability:
1) Define correlation ID mechanism (header + generation)
2) Add structured log conventions (names + context fields)
3) Add scope-aware logging (root/sub-scope)
4) Add duration_ms timing for critical operations
5) Ensure exception mapping to ApiResponse is sanitized
6) Propagate context to Jobs/Listeners
7) Provide tests/examples verifying the behavior

Prefer pragmatic observability that helps debug production incidents
without increasing risk or noise.