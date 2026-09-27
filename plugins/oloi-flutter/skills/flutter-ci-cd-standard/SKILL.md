---
name: flutter-ci-cd-standard
description: Defines CI/CD quality gates and release automation for Flutter apps, including analyze, test, coverage, build validation, and artifact governance.
when_to_use:
  - setting up flutter ci pipeline
  - defining quality gates
  - preparing release workflows
  - preventing regressions before merge
---

# Flutter CI/CD Standard

## Mandatory PR Pipeline

1. `fvm flutter pub get`
2. `fvm dart format --set-exit-if-changed .`
3. `fvm dart analyze`
4. `fvm flutter test --coverage`
5. Build smoke check for target platforms

## Quality Gates

- Pipeline must fail on analyzer errors.
- Pipeline must fail on test failures.
- Enforce minimum coverage threshold agreed by team.
- Keep the SDK pinned through `.fvmrc`; CI must fail when commands bypass FVM.
- Validate local/staging/production flavor configuration without committing secrets.
- Run the smallest applicable gate on every change and the full gate before closure.

## Release Pipeline

- Use versioning policy (SemVer or app-internal standard).
- Generate signed artifacts using secure secrets.
- Keep release notes linked to commit/PR history.
- Manual store upload is acceptable initially, but signed artifacts must still be
  reproducible and produced only from a verified branch/tag.

## Execution Default

For execution-oriented tasks, deliver end-to-end by default:

1. Implement required CI/CD configuration changes.
2. Add/update automated checks and test gates for impacted scope.
3. Update technical documentation required by the change (pipeline behavior, release process, or quality-gate notes when applicable).
4. Report assumptions and verification outcomes.

## Prohibitions

- No manual release from unverified local branch.
- No bypass of analyzer/test gates for production releases.
