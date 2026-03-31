# Release Policy

This repository uses a single codebase with release channels by branch.

## Branches

- `main`: integration branch for accepted PRs.
- `candidate`: pre-release validation channel.
- `stable`: production-ready catalog channel.

## Release Flow

1. Work in feature branches and open PRs to `main`.
2. Promote validated scope from `main` to `candidate`.
3. Run release checks in `candidate`.
4. Promote from `candidate` to `stable`.
5. Create domain tags from `stable`:
   - `laravel-vX.Y.Z`
   - `flutter-vX.Y.Z`
   - `product-analyst-vX.Y.Z`

## Required Release Artifacts

- `manifests/<domain>.json` updated.
- `changelogs/CHANGELOG-<domain>.md` updated.
- Tag created for each released domain.
- Matching plugin package under `plugins/` synchronized from the domain source.
