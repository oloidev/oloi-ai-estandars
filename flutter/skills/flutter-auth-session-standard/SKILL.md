---
name: flutter-auth-session-standard
description: Design and implement secure Sanctum token sessions in Flutter with single-flight refresh, logout isolation, and deterministic lifecycle tests.
when_to_use:
  - implementing login, logout, session bootstrap, or token refresh
  - handling expired sessions or concurrent 401 responses
  - reviewing authentication state boundaries
---

# Flutter Auth Session Standard

Model authentication as an explicit application flow, not as a widget concern.
Use a session repository/manager behind a domain or application port and keep
access/refresh token details out of presentation state when possible.

## Required behavior

- Store credentials only through the secure-storage adapter.
- Bootstrap the session deterministically at app start.
- Attach access tokens through the central Dio interceptor.
- On `401`, run at most one refresh request; concurrent requests await the same
  future and replay only once.
- If refresh fails, clear local credentials, invalidate authenticated providers,
  and navigate to the unauthenticated boundary without a refresh loop.
- Logout is idempotent and clears local state even when the network is unavailable.
- Never log access tokens, refresh tokens, passwords, or raw auth responses.

## Required tests

Cover valid login, invalid credentials, malformed response, bootstrap with no
session, bootstrap with expired session, successful refresh, failed refresh,
concurrent refresh, logout, timeout, cancellation, and stale authenticated UI.

Use fake storage, fake clocks, controllable futures, and a fake transport. Do
not use a live API in unit or widget tests.
