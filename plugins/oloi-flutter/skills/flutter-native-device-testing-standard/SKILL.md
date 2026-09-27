---
name: flutter-native-device-testing-standard
description: Plan and verify Flutter behavior across Android, iOS, tablets, lifecycle, permissions, accessibility, and native UI boundaries.
when_to_use:
  - testing device behavior or native permissions
  - adding lifecycle, notification, deep-link, or platform integration
  - validating tablet/adaptive layouts and accessibility
---

# Flutter Native Device Testing Standard

Use Flutter widget/integration APIs for Flutter behavior and Patrol when the
journey must interact with native dialogs, notifications, settings, or platform
views. Use real devices when hardware, OS behavior, performance, or memory is
part of the claim.

## Matrix

- Android phone and tablet.
- iOS phone and iPad.
- Supported minimum OS and current stable OS.
- Portrait, landscape, text scaling, safe areas, keyboard, and split/resized
  windows where the product supports them.
- Cold start, warm start, background, resume, process death, permission denied,
  permission revoked, notification/deep-link entry, and network loss/recovery.

## Rules

- Base layout decisions on available window constraints, not device-name checks.
- Accessibility tests must cover semantics, labels, target size, contrast, and
  text scaling; manual OS inspectors remain required for release evidence.
- Do not hide flaky device tests with unlimited retries. Record device, OS,
  build, seed, logs, screenshots, and reproduction steps.
- Keep Bluetooth and WebView out of scope unless a SPEC explicitly enables them.
