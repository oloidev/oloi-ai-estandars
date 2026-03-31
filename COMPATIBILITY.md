# Compatibility

## Domains

- Laravel skills are namespaced as `laravel-*` and mapped in `laravel/agents/AGENTS.md`.
- Flutter skills are namespaced as `flutter-*` and mapped in `flutter/agents/AGENTS.md`.
- Product Analyst skills are namespaced as `product-analyst-*` and mapped in `product-analyst/agents/AGENTS.md`.

## Rules

1. Skill folder name must match SKILL frontmatter `name`.
2. Agents must reference existing skill names only.
3. Domain manifests are the source of release truth.
4. Plugin packages under `plugins/` must bundle only skills that belong to their domain.

## Migration Notes

- Legacy Laravel names without prefix were migrated to `laravel-*`.
