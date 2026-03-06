# Infrastructure

## Description
Guides infrastructure-as-code changes across Helm charts, Kubernetes manifests, Bicep templates, Terraform configs, and Dockerfiles.

USE FOR: update helm, modify k8s, change bicep, terraform change, dockerfile update, chart update, deployment config
DO NOT USE FOR: application code changes, test-only changes, documentation-only changes

## Instructions

### When to Apply
When modifying deployment configurations, container images, Helm charts, or Azure resource templates.

### Step-by-Step Procedure
1. **Identify the IaC type:**
   - **Helm charts** → `otelcollector/deploy/chart/`, `otelcollector/deploy/addon-chart/`
   - **Dependent charts** → `otelcollector/deploy/dependentcharts/` (node-exporter, kube-state-metrics)
   - **K8s manifests** → `otelcollector/deploy/`, `otelcollector/customresources/`
   - **Dockerfiles** → `otelcollector/build/linux/`, `otelcollector/build/windows/`
   - **Bicep** → `AddonBicepTemplate/`, `ArcBicepTemplate/`
   - **Terraform** → `AddonTerraformTemplate/`
   - **ARM** → `AddonArmTemplate/`, `ArcArmTemplate/`
   - **Azure Pipelines** → `.pipelines/`
2. **For Helm changes:** Update both `Chart-template.yaml` and `values-template.yaml` as needed.
3. **For Dockerfile changes:** Consider both Linux and Windows variants. Maintain multi-arch support (amd64, arm64).
4. **For Bicep/Terraform:** Update parameter files alongside template changes.
5. **Test deployment** on a dev cluster if possible.
6. **Commit** with format: `build:` or `ci:` prefix as appropriate.

### Files Typically Involved
- `otelcollector/deploy/chart/prometheus-collector/` — main Helm chart
- `otelcollector/build/linux/Dockerfile` — Linux container image
- `otelcollector/build/windows/Dockerfile` — Windows container image
- `AddonBicepTemplate/`, `ArcBicepTemplate/` — Bicep templates
- `.pipelines/` — Azure Pipelines configs

### Validation
- `helm lint` passes on modified charts
- `docker build` succeeds for affected Dockerfiles
- Bicep/Terraform validates without errors
- Deployment to dev cluster succeeds (if available)

## Examples from This Repo
- `3af8b46` — build: update Helm chart for new feature
- `fecaefd` — ci: update Azure Pipeline configuration
- `a99b1ef` — build: update Dockerfile base images
