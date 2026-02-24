---
name: flutter-commit-message-standard
description: Generates professional conventional commit messages for Flutter/Dart work with clear scope, architecture impact, testing notes, and release readiness.
when_to_use:
  - preparing commits after flutter changes
  - generating changelog ready messages
  - enforcing conventional commits in mobile repos
---

# Flutter Commit Message Standard

Use Conventional Commits:

`<type>(<scope>): <short summary>`

## Allowed Types

- `feat`, `fix`, `refactor`, `perf`, `test`, `docs`, `chore`, `build`, `ci`

## Scope Rules

Use feature or layer scope:
- `auth`, `checkout`, `profile`, `networking`, `state`, `testing`, `ci`

## Body Rules (for non-trivial changes)

Include:
- What changed.
- Why architectural decision was made.
- State strategy impact (Riverpod/BLoC).
- Tests added/updated.

## Example

`feat(auth): add token refresh flow with dio interceptor`

- Added typed auth refresh endpoint integration.
- Mapped transport errors to domain failures.
- Added unit tests for token refresh retry behavior.

## Execution Default

For execution-oriented tasks, generate commit output as the final wrap-up after code, tests, and technical documentation updates are complete.
Do not produce a commit message as a substitute for implementation work unless explicitly requested.
