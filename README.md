# OLOI Skills Catalog

Multi-domain skills and agents catalog for Codex.

## Domains

- Laravel: `laravel/`
- Flutter: `flutter/`
- Product Analyst: `product-analyst/`

## Registry Artifacts

- Manifests: `manifests/`
- Changelogs: `changelogs/`
- Release policy: `RELEASE_POLICY.md`
- Compatibility rules: `COMPATIBILITY.md`
- Internal policy: `INTERNAL_USE_POLICY.md`

## Branch Channels

- `main`: integration
- `candidate`: pre-release
- `stable`: production-ready

## Domain Versioning

Tags are domain-scoped:

- `laravel-vX.Y.Z`
- `flutter-vX.Y.Z`
- `product-analyst-vX.Y.Z`

## Plugin Distribution

This repository also acts as a private multi-plugin source for Codex.

- Repo marketplace: `/.agents/plugins/marketplace.json`
- Plugin packages: `/plugins/`

Current plugin packages:

- `oloi-laravel`
- `oloi-flutter`
- `oloi-product-analyst`

Plugin packages under `/plugins/` are distribution artifacts. Domain folders such
as `/laravel/`, `/flutter/`, and `/product-analyst/` remain the source of truth.
