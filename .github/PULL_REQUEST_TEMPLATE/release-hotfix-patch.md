## Hotfix Scope
- Domain: `<laravel|flutter|django|nextjs|design>`
- Patch version: `vX.Y.Z`
- Source branch: `<hotfix/*|candidate>`
- Target branch: `stable`

## Incident / Reason
- Issue link: `<ticket|incident>`
- User impact: `<brief>`
- Severity: `<low|medium|high|critical>`

## Patch Summary
- [ ] Describe the minimal fix applied.
- [ ] Confirm no unrelated scope is included.

## Required Validations (Patch)
- [ ] `manifests/<domain>.json` patch bump only (`X.Y.(Z+1)`)
- [ ] `changelogs/CHANGELOG-<domain>.md` patch entry added
- [ ] Affected skill/agent references verified
- [ ] Targeted smoke test passed
- [ ] Regression risk assessed

## Breaking Changes
- [ ] No breaking changes in patch release
- If exception exists, explain and escalate approval:
  - `<details>`

## Rollback Plan
- Previous stable tag: `<domain>-vX.Y.(Z-1)`
- Rollback command/process documented: `<brief>`

## Tag Plan (post-merge)
- [ ] Create annotated patch tag: `<domain>-vX.Y.Z`

## Approval Request
Please approve this hotfix patch release for immediate stable promotion.
