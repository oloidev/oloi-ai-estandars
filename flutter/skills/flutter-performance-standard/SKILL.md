---
name: flutter-performance-standard
description: Applies modern Flutter performance practices including rebuild minimization, rendering optimization, memory discipline, and profiling-driven improvements.
when_to_use:
  - optimizing flutter performance
  - reducing jank and frame drops
  - improving memory and startup time
  - validating performance regressions
---

# Flutter Performance Standard

Use profiling-first optimization, never guess-first optimization.

## Optimization Workflow

1. Measure baseline (frame times, build counts, memory).
2. Identify hotspot (rebuild storm, layout, paint, async blocking).
3. Apply minimal change.
4. Re-measure and document impact.

## Core Rules

- Use `const` constructors where possible.
- Split heavy widgets.
- Use selective listeners (`select`, granular providers).
- Debounce expensive user-triggered operations.
- Offload heavy compute to isolates when justified.

## List and Scroll Rules

- Prefer lazily built lists.
- Paginate large datasets.
- Avoid nested unbounded scrolls without constraints.

## Execution Default

For execution-oriented tasks, deliver end-to-end by default:

1. Implement targeted performance improvements in impacted code paths.
2. Add/update automated tests where behavior or regressions can be validated.
3. Update technical documentation required by the change (profiling findings, optimization rationale, or performance notes when applicable).
4. Report assumptions and before/after verification outcomes.

## Prohibitions

- No expensive sync parsing on UI thread for large payloads.
- No blanket rebuild patterns for whole page state changes.
