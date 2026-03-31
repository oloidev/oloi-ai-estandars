---
name: flutter-dio-rest-standard
description: Implements REST integrations in Flutter using Dio with typed models, interceptors, robust error mapping, retries, and secure token handling.
when_to_use:
  - integrating rest apis in flutter
  - creating dio clients and interceptors
  - handling auth headers and retries
  - mapping network errors to domain failures
---

# Flutter Dio REST Standard

Use a single Dio client abstraction in `core/networking`.

## Required Components

- `ApiClient` wrapper around Dio.
- Auth interceptor (token injection/refresh strategy).
- Error interceptor (map HTTP/network errors).
- Optional retry/backoff policy for idempotent calls.
- Request/response DTOs per endpoint.

## Mapping Rules

- Data source returns DTOs.
- Repository maps DTOs -> domain entities.
- Never leak Dio exceptions outside data layer.

## Reliability Rules

- Define timeouts explicitly.
- Add cancellation support for user-abandoned requests.
- Implement pagination contracts consistently.

## Security Rules

- No tokens in logs.
- No secrets in source code.
- Use secure storage for credentials.

## Execution Default

For execution-oriented tasks, deliver end-to-end by default:

1. Implement typed Dio integration and error mapping in the correct layers.
2. Add/update automated tests for networking behavior and failure mapping.
3. Update technical documentation required by the change (endpoint contracts, error mapping notes, or integration docs when applicable).
4. Report assumptions and verification outcomes.
