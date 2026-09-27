---
name: flutter-mobile-orchestrator
description: Meta-skill that analyzes a Flutter request and selects the minimum required Flutter skills in the correct execution order, with architecture and risk checks.
when_to_use:
  - planning any non-trivial flutter task
  - coordinating multiple flutter skills
  - producing implementation blueprint before coding
---

# Flutter Mobile Orchestrator

You orchestrate; you do not start coding until the task contract and risk are explicit. In Homu repositories, an approved SPEC is the source of truth. A ticket, prompt, or conversation can provide context but cannot expand SPEC scope.

## Intake Rule

Read `AGENTS.md`, the applicable SPEC, the README, and relevant architecture and testing documents before editing. Extract problem, objective, scope in/out, acceptance criteria, dependencies, risks, and verification commands. Separate confirmed decisions from proposals and stop on missing contracts, permissions, platform requirements, or security decisions.

## Step 1: Classify Request

Classify as one of:
- New feature
- API integration
- State refactor
- Performance optimization
- Security hardening
- Testing expansion
- Release pipeline work
- Bug fix
- Project foundation/bootstrap
- Authentication/session
- Native platform integration
- Accessibility/device quality
- SPEC/code review

## Step 2: Select Skills

Base selection map:

- New feature -> `flutter-architecture-standard` + `flutter-state-management-hybrid` + `flutter-testing-standard`
- API integration -> `flutter-dio-rest-standard` + `flutter-contract-testing-standard` + `flutter-testing-standard`
- Authentication/session -> `flutter-auth-session-standard` + `flutter-dio-rest-standard` + `flutter-security-standard` + `flutter-testing-standard`
- Complex workflow -> `flutter-state-management-hybrid` (BLoC path) + `flutter-testing-standard`
- Performance -> `flutter-performance-standard`
- Security/observability -> `flutter-security-standard` + `flutter-sentry-observability-standard`
- CI/CD/environments -> `flutter-ci-cd-standard` + `flutter-environment-standard`
- Native/device/accessibility -> `flutter-native-device-testing-standard`
- SPEC or closure review -> `flutter-spec-review-standard` + applicable standards
- Commit preparation -> `flutter-commit-message-standard`

## Step 3: Riverpod vs BLoC Decision

- Default Riverpod.
- Promote to BLoC only when event/state complexity is high and justified.

## Step 4: Output Blueprint

Return:
1. Selected skills.
2. Execution order.
3. Architectural risks.
4. Test strategy.
5. Acceptance checklist.

## Execution levels

Name the level before running commands:

1. `focalized`: affected seam and direct tests.
2. `architecture`: format, analyzer, dependency boundaries, and contracts.
3. `suite`: complete local unit/widget/integration-eligible suite.
4. `device`: emulator, simulator, or physical-device validation.
5. `quality`: accessibility, golden, performance, memory, security, mutation, or property tests as applicable.
6. `review`: separate SPEC compliance and engineering standards review.

Do not load every skill for every task. Select the smallest set that covers the risk, and record why a seemingly relevant skill is not applicable.

## Completion Rule

When the request is execution-oriented (feature, fix, refactor, optimization), orchestrate for end-to-end delivery:

1. Code implementation
2. Automated tests (unit/widget/integration as applicable)
3. Documentation updates required by the change
4. Verification summary with risks/assumptions
