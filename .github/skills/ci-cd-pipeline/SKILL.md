# CI/CD Pipeline

## Description
Guides modifications to GitHub Actions workflows, Azure Pipelines, and CI/CD infrastructure.

USE FOR: update workflow, CI/CD changes, pipeline fix, add GitHub Action, update pipeline
DO NOT USE FOR: source code changes, test logic changes, documentation

## Instructions

### When to Apply
When modifying CI/CD pipelines, GitHub Actions workflows, or deployment automation.

### Step-by-Step Procedure
1. Identify the workflow file in `.github/workflows/` or `.pipelines/`
2. GitHub Actions: modify YAML workflow files following existing patterns
3. Azure Pipelines: modify files in `.pipelines/` directory
4. Test workflow changes by triggering a manual run (workflow_dispatch) where available
5. Verify action versions are pinned (Dependabot manages GitHub Actions updates)

### Files Typically Involved
- `.github/workflows/scan.yml` — Trivy image scanning
- `.github/workflows/scan-released-image.yml` — released image scanning
- `.github/workflows/otelcollector-upgrade.yml` — automated OTel upgrades
- `.github/workflows/build-and-release-mixin.yml` — mixin builds
- `.github/workflows/build-and-push-dependent-helm-charts.yml` — Helm chart publishing
- `.github/workflows/size.yml` — PR size labeling
- `.github/workflows/stale.yml` — stale issue management
- `.pipelines/` — Azure Pipelines for deployment

### Validation
- Workflow YAML syntax is valid
- Action versions are pinned to specific versions or SHAs
- Secrets are referenced via `${{ secrets.* }}`, never hardcoded

## Examples from This Repo
- `ci/cd: fixes for tests + release (#1323)`
- `test: Testkube workflow migration (#1392)`
- `fix: helm lint+dry-run check for PRs + arc fixes + build fixes (#1326)`
