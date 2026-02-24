## Release Scope
- Domain: `<laravel|flutter|django|nextjs|design>`
- Target version: `vX.Y.Z`
- Source branch: `main`
- Target branch: `candidate`

## Summary of Changes
- [ ] List key skill/agent changes included in this promotion.
- [ ] Mention refactors, new skills, or naming updates.

## Manifest Validation
- [ ] `manifests/<domain>.json` exists
- [ ] `version` updated to `X.Y.Z`
- [ ] `released_at` updated
- [ ] `skills[]` matches real folders under `<domain>/skills/`
- [ ] `deprecated` updated if applicable
- [ ] `breaking_changes` updated if applicable

## Changelog Validation
- [ ] `changelogs/CHANGELOG-<domain>.md` has entry `[X.Y.Z] - YYYY-MM-DD`
- [ ] Added/Changed/Deprecated/Removed/Fixed sections reviewed
- [ ] Breaking changes documented (or N/A)

## Domain Consistency Checks
- [ ] All skill names are prefixed with `<domain>-`
- [ ] Skill folder name matches `name:` in each `SKILL.md`
- [ ] `agents/<domain>/AGENTS.md` references existing skills only
- [ ] No legacy non-prefixed skill names remain in domain docs

## Risk and Compatibility
- Breaking changes: `<Yes|No>`
- Migration required: `<Yes|No>`
- Compatibility notes updated in `COMPATIBILITY.md`: `<Yes|No|N/A>`

## Candidate Validation Plan
- [ ] Install 1-2 representative skills from candidate branch/tag
- [ ] Verify Codex detects them by name/trigger
- [ ] Verify agent-to-skill routing for the domain

## Approval Request
Please review for candidate promotion readiness.
