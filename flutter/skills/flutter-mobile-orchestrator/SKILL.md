---
name: flutter-mobile-orchestrator
description: Meta-skill that analyzes a Flutter request and selects the minimum required Flutter skills in the correct execution order, with architecture and risk checks.
when_to_use:
  - planning any non-trivial flutter task
  - coordinating multiple flutter skills
  - producing implementation blueprint before coding
---

# Flutter Mobile Orchestrator

You orchestrate; you do not start coding yet.

## Intake Rule

Assume input is a copied Asana task with context about problem, goal, and constraints.
Use that ticket as primary scope definition and infer the best technical approach by default.
Assume full execution is expected: implementation + tests + technical documentation updates when applicable.

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

## Step 2: Select Skills

Base selection map:

- New feature -> `flutter-architecture-standard` + `flutter-state-management-hybrid` + `flutter-testing-standard`
- API integration -> `flutter-dio-rest-standard` + `flutter-testing-standard`
- Complex workflow -> `flutter-state-management-hybrid` (BLoC path) + `flutter-testing-standard`
- Performance -> `flutter-performance-standard`
- Security -> `flutter-security-standard`
- CI/CD -> `flutter-ci-cd-standard`
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

## Completion Rule

When the request is execution-oriented (feature, fix, refactor, optimization), orchestrate for end-to-end delivery:

1. Code implementation
2. Automated tests (unit/widget/integration as applicable)
3. Documentation updates required by the change
4. Verification summary with risks/assumptions
