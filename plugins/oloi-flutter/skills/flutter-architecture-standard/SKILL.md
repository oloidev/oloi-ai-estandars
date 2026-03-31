---
name: flutter-architecture-standard
description: Designs and implements Flutter features using a feature-first Clean Architecture (presentation, application, domain, data) with clear boundaries and testable code.
when_to_use:
  - creating a new flutter feature
  - refactoring architecture
  - defining module boundaries
  - enforcing clean architecture in mobile apps
---

# Flutter Architecture Standard

Use a feature-first Clean Architecture:

- `presentation`: UI, routing adapters, state bindings
- `application`: use cases and orchestration
- `domain`: entities, repository contracts, value objects
- `data`: DTO/models, remote/local data sources, repository implementations

## Rules

1. UI never depends on Dio or concrete data sources.
2. Domain has no Flutter imports.
3. Application coordinates use cases and maps failures to UI-safe states.
4. Data layer maps external schemas to domain entities.

## Delivery Checklist

- Create feature folder structure.
- Define domain entities and repository interfaces first.
- Implement use cases in application layer.
- Implement repository adapters in data layer.
- Wire dependencies through Riverpod providers.
- Add unit tests for use cases and mapping code.

## Execution Default

For execution-oriented tasks, deliver end-to-end by default:

1. Implement architecture-compliant code changes.
2. Add/update automated tests for impacted behavior.
3. Update technical documentation required by the change (architecture notes, code docs, or API contracts when applicable).
4. Report assumptions and verification outcomes.

## Prohibitions

- No business rules in Widgets.
- No direct JSON handling in presentation layer.
- No cross-feature imports without explicit interface.
