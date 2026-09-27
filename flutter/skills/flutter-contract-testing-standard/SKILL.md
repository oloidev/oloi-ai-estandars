---
name: flutter-contract-testing-standard
description: Validate Flutter API clients against the backend OpenAPI contract and typed response/error envelopes without inventing payloads.
when_to_use:
  - integrating a backend endpoint in Flutter
  - generating or validating DTOs
  - preventing API contract drift
---

# Flutter Contract Testing Standard

Treat the backend OpenAPI document and implementation as the authority. Do
not invent fields, permissions, envelopes, status codes, or pagination rules.
If the contract is ambiguous, stop and request a backend decision or dependent
SPEC.

## Required checks

- Validate request paths, methods, parameters, headers, auth, and content types.
- Validate success envelopes, nullable fields, enum values, and error schemas.
- Cover the applicable `200/201`, `401`, `403`, `404`, `409`, `422`, `429`, `5xx`,
  timeout, malformed response, and cancellation scenarios.
- Keep DTOs in data and map them to domain entities before application/UI use.
- Use deterministic fixtures or a local mock server; unit/widget tests never
  call the network.
- Record the OpenAPI source, revision, fixture files, command, and drift result
  in the SPEC evidence.
