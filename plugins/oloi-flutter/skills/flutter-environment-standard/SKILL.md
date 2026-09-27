---
name: flutter-environment-standard
description: Configure reproducible Flutter environments with FVM, local Docker API endpoints, flavors, and secret-safe dart defines.
when_to_use:
  - bootstrapping a Flutter repository
  - configuring local, staging, or production environments
  - changing SDK, flavor, bundle, or API base URL settings
---

# Flutter Environment Standard

Pin the project Flutter SDK in `.fvmrc` and run Flutter/Dart commands through
FVM. Keep environment selection explicit and make the default safe for local
development.

## Rules

- Commit `.fvmrc` and the package lockfile.
- Separate `local`, `staging`, and `production` configuration.
- Inject public configuration with `--dart-define` or a checked-in example
  file; never treat a client-side value as a secret.
- Local Docker HTTP is allowed only in the local flavor. Staging/production
  must enforce HTTPS.
- Keep bundle identifiers, app names, and signing configuration explicit per
  flavor/platform.
- Tests must be able to override configuration without reading developer
  machines, real credentials, or production services.
- Document the first command, last verification command, and recovery path for
  a clean checkout.
