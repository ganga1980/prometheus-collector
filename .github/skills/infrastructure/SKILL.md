# Infrastructure

## Description
Modify Helm charts, Dockerfiles, Kubernetes manifests, and IaC templates (Bicep, Terraform, ARM).

USE FOR: update Helm chart, modify Dockerfile, change Kubernetes manifest, update Bicep template, update Terraform, add deployment config
DO NOT USE FOR: application logic changes, test authoring, CI/CD pipeline changes

## Instructions

### When to Apply
When modifying infrastructure-as-code, container images, or deployment configurations (123 commits/year, 45.4%).

### Step-by-Step Procedure
1. **Identify scope:**
   - **Helm charts:** `otelcollector/deploy/addon-chart/` (AKS addon) or `otelcollector/deploy/chart/` (standalone)
   - **Dockerfiles:** `otelcollector/build/linux/Dockerfile` or `otelcollector/build/windows/Dockerfile`
   - **Bicep:** `AddonBicepTemplate/` or `ArcBicepTemplate/`
   - **Terraform:** `AddonTerraformTemplate/`
   - **ARM:** `AddonArmTemplate/` or `ArcArmTemplate/`
2. **Check both chart paths** — Changes to addon-chart often need mirroring in chart/prometheus-collector.
3. **Check both platforms** — Linux and Windows Dockerfiles must stay in sync for feature parity.
4. **Update values.yaml** — Add new Helm values with sensible defaults.
5. **Validate:**
   - `helm lint <chart-path>`
   - `helm template <chart-path>` to preview rendered manifests
   - `az bicep build --file <file>` for Bicep validation

### Files Typically Involved
- `otelcollector/deploy/addon-chart/azure-monitor-metrics-addon/` (templates, values.yaml)
- `otelcollector/deploy/chart/prometheus-collector/` (templates, values.yaml)
- `otelcollector/build/linux/Dockerfile`, `otelcollector/build/windows/Dockerfile`
- `otelcollector/configmapparser/default-prom-configs/*.yml`
- `AddonBicepTemplate/*.bicep`, `AddonTerraformTemplate/*.tf`

### Validation
- Helm lint passes
- Helm template renders valid YAML
- Docker build succeeds for target architecture
- No high-cardinality labels in scrape configs
- IaC templates are syntactically valid

## Examples from This Repo
- `Enable DCGM exporter by default and optimize label handling (#1417)`
- `Remove nodes/proxy from clusterrole (#1418)`
- `fix: Correct node affinity syntax in ama-metrics DS`

## References
- `otelcollector/deploy/` — All deployment artifacts
- `AddonBicepTemplate/`, `AddonTerraformTemplate/` — IaC templates
