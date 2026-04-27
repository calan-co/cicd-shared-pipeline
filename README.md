# cicd-shared-pipeline
Central reusable CI/CD workflows and shared pipeline actions for calan-co repositories

## Repository Policy Pack

This repo includes reusable repository-level ruleset definitions and scripts to apply
them across repositories when org-level rulesets are unavailable.

### Policy Files

- `policies/01-long-lived-branch-policy.json`
- `policies/02-staging-merge-method-policy.json`
- `policies/03-main-merge-method-policy.json`
- `policies/04-release-tag-policy.json`

The branch policy files align with the templjs rigor profile: required reviews,
stale-review dismissal, review-thread resolution, merge-method constraints,
required status checks, and Copilot code review.

### Scripts

- `scripts/apply-repo-policies.sh`: Apply or update all policy files for one target repo
- `scripts/apply-org-public-repo-policies.sh`: Discover all public repos in an org and call the single-repo script

### Usage

Apply to one repository:

```bash
./scripts/apply-repo-policies.sh --repo calan-co/doc-vader
```

Apply to all public repositories in an org:

```bash
./scripts/apply-org-public-repo-policies.sh --org calan-co
```

Dry-run mode (no changes):

```bash
./scripts/apply-org-public-repo-policies.sh --org calan-co --dry-run
```

## Reusable Release Workflow

This repo now provides a reusable package-release workflow for Node/pnpm packages:

- `.github/workflows/reusable-node-package-release.yml`
- `.github/actions/setup-workspace/action.yml`

Consumer repos keep a thin caller workflow that references this file via `uses:`.
An example caller is provided here:

- `.github/examples/release-caller.yml`

GitHub's native organization workflow template picker expects templates to live in
the special `ORG/.github` repository. The release workflow template now lives in
`calan-co/.github` under `.github/workflow-templates/`.

Minimal caller shape:

```yaml
env:
	SHARED_RELEASE_REF: v1
	PACKAGE_NAME: '@calan-co/doc-vader'

jobs:
	version:
		uses: calan-co/cicd-shared-pipeline/.github/workflows/reusable-node-package-release.yml@v1
		secrets: inherit
		with:
			mode: version
			package-name: ${{ env.PACKAGE_NAME }}
```
