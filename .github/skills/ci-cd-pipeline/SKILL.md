# CI/CD Pipeline

## Description
Modify GitHub Actions workflows and Azure Pipelines configurations.

USE FOR: update pipeline, fix CI, add workflow, modify build, update nightly tests, fix pipeline failure
DO NOT USE FOR: application code changes, Helm chart logic, dependency updates

## Instructions

### When to Apply
When modifying CI/CD pipelines, build processes, or test automation infrastructure.

### Step-by-Step Procedure
1. **Identify the pipeline:**
   - **GitHub Actions:** `.github/workflows/` (scan, mixin build, OTel upgrade, Helm chart release, stale issues, PR size)
   - **Azure Pipelines:** `.pipelines/` (main build, release, nightly tests, AKS deploy, regional tests, config tests)
2. **Make changes** — Edit the YAML pipeline file.
3. **Validate syntax** — YAML linting for correct indentation and structure.
4. **Test locally if possible** — Use `act` for GitHub Actions or review Azure Pipeline YAML schema.

### Files Typically Involved
- `.github/workflows/*.yml` — GitHub Actions
- `.pipelines/azure-pipeline-*.yml` — Azure Pipelines
- `.pipelines/OneBranch.Official.yml` — Official build
- `otelcollector/build/linux/Dockerfile`, `otelcollector/build/windows/Dockerfile`
- `otelcollector/test/testkube/` — TestKube integration

### Validation
- YAML syntax is valid
- Pipeline triggers are correctly scoped (branch filters, path filters)
- No secrets hardcoded in pipeline files
- Commit message: `ci/cd: <description>` or `test: <description>`

## Examples from This Repo
- `test: Testkube workflow migration (#1392)`
- `test: fix nightly build (#1409)`
- `fix: helm lint+dry-run check for PRs + arc fixes + build fixes (#1326)`

## References
- `.github/workflows/` — GitHub Actions workflows
- `.pipelines/` — Azure Pipelines configurations
