# CI/CD Pipeline

## Description
Guides modifications to Azure Pipelines and GitHub Actions workflows used for building, testing, scanning, and deploying the prometheus-collector.

USE FOR: update pipeline, CI change, workflow change, build pipeline, fix build, update Azure Pipeline, GitHub Actions
DO NOT USE FOR: application code changes, Helm chart logic, test code, documentation

## Instructions

### When to Apply
When modifying CI/CD build definitions, adding new pipeline stages, updating build variables, or fixing pipeline failures.

### Step-by-Step Procedure
1. **Identify the pipeline**:
   - **Azure Pipelines** (primary CI): `.pipelines/azure-pipeline-build.yml` — builds images, packages Helm charts, deploys to dev
   - **Azure Pipelines** (1ES): `.pipelines/OneBranch.Official.yml` — official 1ES pipeline
   - **Azure Pipelines** (tests): `.pipelines/azure-pipeline-nightly-tests.yml`, `azure-pipeline-config-tests.yml`
   - **Azure Pipelines** (release): `.pipelines/azure-pipeline-release.yml`, `azure-pipeline-arc-release.yml`
   - **GitHub Actions**: `.github/workflows/scan.yml` (Trivy image scan), `build-and-push-dependent-helm-charts.yml`
2. **Modify** the pipeline YAML. Key variables:
   - `GOLANG_VERSION` — Go version for builds
   - `FLUENTBIT_GOLANG_VERSION` — Go version for Fluent Bit plugin
   - `FLUENT_BIT_VERSION` — Fluent Bit base version
   - `PROMETHEUS_VERSION` — Prometheus version
   - `HELM_VERSION` — Helm CLI version
3. **Validate YAML syntax** before committing.
4. **Test**: Trigger a PR build to validate the pipeline change.

### Files Typically Involved
- `.pipelines/azure-pipeline-build.yml` — main build pipeline
- `.pipelines/OneBranch.Official.yml` — 1ES official pipeline
- `.github/workflows/*.yml` — GitHub Actions
- `.pipelines/deployment/` — EV2 deployment artifacts

### Validation
- Pipeline YAML is valid (no syntax errors)
- PR build triggers and completes successfully
- No regression in build/test/scan stages

## Examples from This Repo
- `fix: manifest list issue on arm host, testkube golang version upgrade` (bed5ae1)
- `target allocator move to imagetools` (4c20aa7)
- Pipeline variable updates for `GOLANG_VERSION`, `FLUENT_BIT_VERSION`

## References
- `.pipelines/azure-pipeline-build.yml` — primary build definition
- `.github/workflows/` — GitHub Actions workflows
