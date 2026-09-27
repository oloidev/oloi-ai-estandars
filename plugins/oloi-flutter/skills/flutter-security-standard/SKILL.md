---
name: flutter-security-standard
description: Enforces secure Flutter mobile practices including secret management, secure token storage, transport hardening, and privacy-safe observability.
when_to_use:
  - hardening flutter app security
  - implementing auth/session handling
  - preventing data leakage in logs
  - reviewing mobile security posture
---

# Flutter Security Standard

## Identity and Session

- Use secure token storage.
- Keep access token lifecycle explicit.
- Handle refresh flows with replay protection.
- Logout must revoke/clear the session locally and invalidate dependent state.
- A refresh failure must fail closed and must not loop or retain stale credentials.

## Transport Security

- Enforce HTTPS only.
- Validate certificate strategy explicitly.
- Reject insecure fallback configurations in production.

## App Security Hygiene

- No API keys/secrets hardcoded in source.
- Use environment-based configuration injection.
- Sanitize logs and crash reports (no PII/tokens).
- Configure Sentry before production use with scrubbing rules for tokens,
  authorization headers, passwords, personal identifiers, and raw API payloads.

## Data Safety

- Minimize local sensitive data persistence.
- Encrypt sensitive local payloads when storage is required.
- Define data retention and cleanup behavior.

## Validation

- Validate and sanitize all remote input before domain mapping.
- Fail closed on malformed security-sensitive payloads.
- Test unauthorized, expired-session, refresh-race, logout, and malformed-token cases.

## Execution Default

For execution-oriented tasks, deliver end-to-end by default:

1. Implement security hardening in impacted flows and layers.
2. Add/update automated tests for security-relevant behavior where applicable.
3. Update technical documentation required by the change (security assumptions, threat notes, or hardening decisions when applicable).
4. Report assumptions and verification outcomes.
