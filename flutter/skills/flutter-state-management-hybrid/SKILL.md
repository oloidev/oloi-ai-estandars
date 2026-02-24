---
name: flutter-state-management-hybrid
description: Applies a hybrid state strategy where Riverpod is the default and BLoC is used only for complex workflows requiring explicit event-state modeling.
when_to_use:
  - selecting state management approach
  - implementing riverpod state flows
  - handling complex workflows with bloc
  - defining boundaries between riverpod and bloc
---

# Flutter State Management Hybrid

Default rule:
- Use Riverpod for most features.

Escalation rule:
- Use BLoC for complex workflows with many events, cross-step transitions, or strict auditability of transitions.

## Decision Matrix

Use Riverpod when:
- Simple to medium CRUD flows.
- Derived state and async state with low event complexity.

Use BLoC when:
- Multi-step transactional flows.
- Concurrent event streams and explicit transition control.
- Team needs deterministic event -> state traces.

## Integration Standard

- Keep one state owner per feature flow.
- If both are used in app, isolate by feature.
- Riverpod may provide BLoC instances, but feature logic lives in one paradigm.

## Testing

- Riverpod: provider tests + widget tests.
- BLoC: bloc tests for transition matrix + widget tests.

## Execution Default

For execution-oriented tasks, deliver end-to-end by default:

1. Implement state flow changes using the selected paradigm (Riverpod by default, BLoC when justified).
2. Add/update provider or bloc tests plus widget coverage for impacted states.
3. Update technical documentation required by the change (state decisions, transition notes, or feature design notes when applicable).
4. Report assumptions and verification outcomes.

## Prohibitions

- Do not mix Riverpod and BLoC in the same screen flow without explicit architecture notes.
