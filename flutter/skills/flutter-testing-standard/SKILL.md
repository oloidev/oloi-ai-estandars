---
name: flutter-testing-standard
description: Defines enterprise Flutter testing strategy with unit, widget, and integration tests, including coverage targets, test pyramids, and CI enforcement.
when_to_use:
  - writing flutter tests
  - improving automated coverage
  - defining test pyramid in mobile apps
  - adding quality gates in ci
---

# Flutter Testing Standard

Apply this test pyramid:

1. Unit tests (majority): domain rules, use cases, mappers.
2. Widget tests (medium): UI behavior and state rendering.
3. Integration tests (selective): critical user journeys.

Add selective quality layers when the change warrants them:

- Contract tests against the backend OpenAPI/schema.
- Architecture/import tests.
- Golden tests for stable, high-risk visual surfaces.
- Accessibility guideline and semantics tests.
- Native-device tests for permissions, notifications, lifecycle, and platform views.
- Property tests for domain invariants.
- Mutation tests for critical domain/application code.
- Profile-mode performance and memory tests on real devices.

## Coverage Policy

- Domain/Application: >= 85%.
- New critical flow: must include integration test.
- Critical domain/application diff: mutation or an explicit non-applicability decision.
- Device-facing change: emulator/simulator coverage plus physical-device evidence when hardware behavior matters.

## Suite Requirements per Feature

- Happy path.
- Validation and error paths.
- Empty/loading/retry UI states.
- Authorization/session-expiry cases when applicable.

## Tooling

- `flutter test` for unit/widget.
- `flutter test integration_test` for integration flows.
- `fvm flutter test` and `fvm dart analyze` when FVM is configured.
- Deterministic fixtures and fake repositories where possible.
- Patrol is preferred when a journey must interact with native platform UI; use
  the official `integration_test` package for Flutter-only device journeys.

## Execution Default

For execution-oriented tasks, deliver end-to-end by default:

1. Implement the required code changes first or in parallel with tests.
2. Add/update unit, widget, and integration tests according to impact.
3. Update technical documentation required by the change (test notes, coverage decisions, or behavior specs when applicable).
4. Report assumptions and verification outcomes.

## Prohibitions

- No flaky timer-dependent tests without fake clock.
- No network calls in unit/widget tests.
- No sleeps as synchronization; use fake clocks, controllable futures, and
  lifecycle/test bindings.
- Do not treat coverage percentage as proof of behavioral completeness.
- Do not update goldens merely to hide a regression; record the visual decision.
