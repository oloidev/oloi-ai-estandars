# CHANGELOG - Flutter Skills

All notable changes to the Flutter skills catalog are documented in this file.

## [1.1.0] - 2026-09-26
### Added
- Auth/session standard for Sanctum token refresh, logout isolation, and deterministic lifecycle tests.
- Contract testing standard for OpenAPI-driven DTO and error validation.
- Environment standard for FVM, flavors, local Docker API configuration, and secret-safe defines.
- Native device testing standard for Android, iOS, tablets, lifecycle, accessibility, and platform UI.
- Sentry observability standard with privacy-safe scrubbing requirements.
- SPEC review standard separating scope compliance from engineering standards.
- CI/CD and review agent profiles for reproducible gates and AI-regression detection.

### Changed
- The mobile orchestrator now uses approved SPECs as the source of truth and selects skills by risk.
- Architecture, Dio, testing, security, and CI/CD standards now include FVM, session, contract, device, and evidence requirements.

### Breaking Changes
- Asana/ticket context no longer overrides an approved SPEC or repository `AGENTS.md`.

## [1.0.0] - 2026-02-24
### Added
- Initial Flutter skills catalog published.
- Hybrid state strategy documented (Riverpod default, BLoC for complex workflows).
- End-to-end delivery rules enforced (implementation + tests + documentation).

### Changed
- N/A

### Deprecated
- N/A

### Removed
- N/A

### Fixed
- N/A

### Breaking Changes
- N/A
