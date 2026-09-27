---
name: flutter-spec-review-standard
description: Review Flutter changes against an approved SPEC and engineering standards, separating scope compliance from architecture and quality findings.
when_to_use:
  - reviewing or closing a Flutter SPEC
  - preparing a merge decision
  - auditing AI-generated changes for drift or scope creep
---

# Flutter SPEC Review Standard

Review two independent axes. A green test suite does not prove that the
implementation follows the requested scope, and a compliant SPEC does not
prove that the architecture is safe.

## Spec axis

- SPEC is Approved and the work ID/base are valid.
- Every acceptance criterion has implementation and evidence.
- Diff stays inside Scope In.
- No invented API, permission, UX state, platform capability, or dependency.
- Deferred cases and pre-existing failures are explicit.

## Standards axis

- Dependency direction and feature boundaries are preserved.
- FVM, flavors, secrets, auth, Sentry, and platform boundaries are safe.
- Loading, empty, error, retry, session-expiry, lifecycle, and accessibility
  states are covered where applicable.
- Tests are deterministic and non-tautological.
- Device/performance/security evidence matches the claim.

Each blocker must include file/line or concrete reference, violated rule,
evidence, and required correction. Re-run affected gates after correction.
