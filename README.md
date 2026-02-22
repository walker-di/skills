# walker-di/skills

Public publishing repository for Fortalece skills bundles.

## Publishing Workflow

Workflow: `.github/workflows/publish-fortalece-skills.yml`

Trigger mode: manual (`workflow_dispatch`) only.

### Required Secret

- `SKILL_REPO_TOKEN`
  - read access to `Walker-D-I/pic-pay`
  - write access to `walker-di/skills`

### Manual Run

1. Open GitHub Actions in this repository.
2. Select **Publish Fortalece Skills**.
3. Click **Run workflow**.
4. Configure inputs:
   - `source_ref`: branch/tag/SHA in `Walker-D-I/pic-pay` (default: `main`)
   - `dry_run`:
     - `true`: run checks/build and show diff without push
     - `false`: run checks/build and push updates to `main` when diff exists

### What the Workflow Does

1. Checks out this repository (`main`).
2. Checks out `Walker-D-I/pic-pay` at the selected `source_ref`.
3. Runs:
   - `npm run check:fortalece-codex-skill-routing`
   - `npm run skills:check-public`
   - `npm run skills:prepare-public`
4. Copies generated bundle from:
   - `_src/pic-pay/artifacts/fortalece-ai-skills-public`
5. Publishes to:
   - `fortalece-ai-skills-public/`
6. Commits with:
   - `user.name=skills-ci-bot`
   - `user.email=ci@walker-di.com.br`
   - message: `chore(skills): sync from pic-pay <short_sha>`

### Published Bundle Contract

`fortalece-ai-skills-public/` must contain:

- `manifest.json`
- `README.md`
- `skills/**`

### Failure Conditions

- missing `SKILL_REPO_TOKEN`
- source checkout/auth failure
- stale routing artifacts in source repo
- skills readiness validation failure
- missing generated bundle
- push rejected by branch protection
