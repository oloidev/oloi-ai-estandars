# OLOI Flutter & Dart AI Standards

## Project Context

This mobile stack is built using:

- Flutter 3.x
- Dart 3.x
- Riverpod (default state management)
- BLoC (for complex business workflows)
- Dio for REST networking
- go_router for navigation
- Freezed + json_serializable for immutable models
- CI/CD with quality gates

This is a production-grade mobile architecture.
All generated code must follow enterprise standards.

---

# Core Architectural Principles

1. Clean Architecture
2. SOLID principles
3. Feature-first modular organization
4. Strict separation of concerns
5. Explicit typing and immutability
6. Test-first mindset
7. Performance awareness
8. Security by default

No business rules in Widgets.
No direct networking logic in UI layer.

---

# Standard Module Structure

All new features must use feature-first organization:

lib/features/{feature_name}/
    presentation/
        pages/
        widgets/
        controllers/
    application/
        usecases/
        services/
    domain/
        entities/
        repositories/
        value_objects/
    data/
        datasources/
        models/
        repositories/

Shared code:

lib/core/
    networking/
    errors/
    result/
    utils/
    logging/

---

# State Management Standard

Default:
- Riverpod for app and feature state.

Use BLoC only when:
- Workflow is highly complex.
- Multiple asynchronous event streams must be coordinated.
- A formal event->state transition model is required.

Never mix Riverpod and BLoC inside the same feature flow without explicit boundaries.

---

# Networking Standard

- Use Dio through a single HTTP client abstraction.
- Always define typed request/response models.
- Centralize interceptors (auth, retry, logging, tracing).
- Map transport errors to domain-safe failures.
- No raw Dio calls inside Widgets.

---

# Testing Standard

Every new feature must include:

- Unit tests (domain/application)
- Widget tests (presentation behavior)
- Integration tests (critical user journeys)

Coverage targets:
- Domain/Application: >= 85%
- Critical flows: mandatory integration coverage

---

# Performance Rules

- Keep widgets small and const where possible.
- Avoid unnecessary rebuilds (provider selectors, memoization).
- Use pagination/lazy loading for large lists.
- Avoid expensive work on UI thread.
- Profile before and after optimization.

---

# Security Rules

- Never store secrets in code.
- Use secure storage for tokens.
- Enforce TLS and safe certificate handling.
- Sanitize logs (no PII/tokens).
- Validate all remote input.

---

# Code Quality Rules

- Strict lints enabled.
- No dead code.
- No unused imports.
- Clear naming conventions.
- Explicit error handling.
- Small, testable units.

---

# Skill Selection Strategy

When user requests:

- "Create Flutter feature"
  -> Load flutter-architecture-standard.
- "Manage state"
  -> Load flutter-state-management-hybrid.
- "Connect API"
  -> Load flutter-dio-rest-standard.
- "Write tests"
  -> Load flutter-testing-standard.
- "Improve performance"
  -> Load flutter-performance-standard.
- "Harden security"
  -> Load flutter-security-standard.
- "Setup pipeline"
  -> Load flutter-ci-cd-standard.
- "Generate commit"
  -> Load flutter-commit-message-standard.

Use flutter-mobile-orchestrator when the request spans multiple architectural dimensions.

---

# Task Intake Standard

Input will usually be an Asana task copied by the developer, including business context, symptoms, and constraints.

Agent must:

1. Treat the Asana ticket as the source of truth for scope and intent.
2. Infer the best applicable technical strategy by default (architecture, state, networking, testing, performance, security).
3. Apply enterprise best practices automatically for each task type: feature, fix, refactor, or tests.
4. Prefer robust and maintainable implementations over shortcuts unless constraints explicitly require otherwise.

---

# End-to-End Delivery Standard

For any development request with sufficient context, the default expectation is full delivery, not partial guidance.

Agent and skills must:

1. Interpret the task and implement the required code changes.
2. Cover validation of behavior through appropriate tests.
3. Include/update technical documentation needed to maintain the code (inline docs, architecture notes, API docs, test notes) when applicable.
4. Report assumptions, risks, and verification results clearly.

Do not stop at analysis unless explicitly requested by the developer.
Do not skip tests or documentation updates for production-facing changes unless explicitly instructed.

This standard applies to all Flutter skills in this repository.

---

# Strict Prohibitions

- No business logic inside Widgets.
- No Dio calls inside presentation widgets.
- No untyped dynamic JSON propagation across layers.
- No skipping tests for production features.
- No logging of sensitive user data.

---

## Skills

A skill is a set of local instructions stored in a `SKILL.md` file.

### Available skills

- flutter-architecture-standard: Designs and implements Flutter features using a feature-first Clean Architecture (presentation, application, domain, data) with clear boundaries and testable code. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/skills/flutter-architecture-standard/SKILL.md)
- flutter-state-management-hybrid: Applies a hybrid state strategy where Riverpod is the default and BLoC is used only for complex workflows requiring explicit event-state modeling. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/skills/flutter-state-management-hybrid/SKILL.md)
- flutter-dio-rest-standard: Implements REST integrations in Flutter using Dio with typed models, interceptors, robust error mapping, retries, and secure token handling. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/skills/flutter-dio-rest-standard/SKILL.md)
- flutter-testing-standard: Defines enterprise Flutter testing strategy with unit, widget, and integration tests, including coverage targets, test pyramids, and CI enforcement. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/skills/flutter-testing-standard/SKILL.md)
- flutter-performance-standard: Applies modern Flutter performance practices including rebuild minimization, rendering optimization, memory discipline, and profiling-driven improvements. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/skills/flutter-performance-standard/SKILL.md)
- flutter-security-standard: Enforces secure Flutter mobile practices including secret management, secure token storage, transport hardening, and privacy-safe observability. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/skills/flutter-security-standard/SKILL.md)
- flutter-ci-cd-standard: Defines CI/CD quality gates and release automation for Flutter apps, including analyze, test, coverage, build validation, and artifact governance. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/skills/flutter-ci-cd-standard/SKILL.md)
- flutter-commit-message-standard: Generates professional conventional commit messages for Flutter/Dart work with clear scope, architecture impact, testing notes, and release readiness. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/skills/flutter-commit-message-standard/SKILL.md)
- flutter-mobile-orchestrator: Meta-skill that analyzes a Flutter request and selects the minimum required Flutter skills in the correct execution order, with architecture and risk checks. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/skills/flutter-mobile-orchestrator/SKILL.md)

---

## Agents

An agent is a role-specialized execution profile that coordinates one or more skills.

### Available agents

- flutter-orchestrator-agent: End-to-end coordinator for Asana tasks. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/agents/roles/flutter-orchestrator-agent.md)
- flutter-architect-agent: Clean Architecture and modular boundaries authority. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/agents/roles/flutter-architect-agent.md)
- flutter-feature-agent: Product feature implementation owner. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/agents/roles/flutter-feature-agent.md)
- flutter-api-agent: Dio REST integration and reliability owner. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/agents/roles/flutter-api-agent.md)
- flutter-test-agent: Automated testing strategy and coverage owner. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/agents/roles/flutter-test-agent.md)
- flutter-performance-agent: Profiling-first optimization owner. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/agents/roles/flutter-performance-agent.md)
- flutter-security-agent: Mobile security hardening owner. (file: /Users/kamikasu/oloi/oloi-ai-estandars/flutter/agents/roles/flutter-security-agent.md)

### Agent Routing Strategy

- Feature or bug fix with normal complexity:
  - `flutter-orchestrator-agent` + `flutter-feature-agent`
- Complex architecture or large refactor:
  - add `flutter-architect-agent`
- Heavy API or networking work:
  - add `flutter-api-agent`
- Test-heavy tasks or quality hardening:
  - add `flutter-test-agent`
- Performance incident or optimization task:
  - add `flutter-performance-agent`
- Security-sensitive scope:
  - add `flutter-security-agent`
