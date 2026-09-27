---
name: flutter-sentry-observability-standard
description: Configure privacy-safe Sentry observability for Flutter errors, performance, releases, and session context.
when_to_use:
  - adding or reviewing Sentry integration
  - defining crash and performance observability
  - debugging production failures without leaking sensitive data
---

# Flutter Sentry Observability Standard

Sentry is diagnostic telemetry, never a source of business truth. Configure it
once at the app boundary and keep domain/application code independent of the
SDK through a small observability port when feature code needs breadcrumbs or
events.

## Required controls

- Configure DSN and environment through injected public configuration; do not
  commit secrets or environment-specific credentials.
- Scrub authorization headers, tokens, passwords, personal identifiers, raw
  request/response bodies, and sensitive breadcrumbs before sending.
- Attach release, build number, platform, flavor, and correlation ID when safe.
- Treat user identity and custom context as opt-in and privacy-reviewed.
- Test that intentional exceptions are captured without sensitive payloads and
  that disabled/local telemetry does not contact Sentry.
- Document sampling, retention, alert ownership, and recovery expectations.
