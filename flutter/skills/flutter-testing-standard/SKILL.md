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

## Coverage Policy

- Domain/Application: >= 85%.
- New critical flow: must include integration test.

## Suite Requirements per Feature

- Happy path.
- Validation and error paths.
- Empty/loading/retry UI states.
- Authorization/session-expiry cases when applicable.

## Tooling

- `flutter test` for unit/widget.
- `flutter test integration_test` for integration flows.
- Deterministic fixtures and fake repositories where possible.

## Execution Default

For execution-oriented tasks, deliver end-to-end by default:

1. Implement the required code changes first or in parallel with tests.
2. Add/update unit, widget, and integration tests according to impact.
3. Update technical documentation required by the change (test notes, coverage decisions, or behavior specs when applicable).
4. Report assumptions and verification outcomes.

## Prohibitions

- No flaky timer-dependent tests without fake clock.
- No network calls in unit/widget tests.
