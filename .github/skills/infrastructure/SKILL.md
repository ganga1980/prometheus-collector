# Infrastructure

## Description
Guides changes to deployment infrastructure including Dockerfiles, Helm charts, Bicep templates, ARM templates, and Kubernetes manifests.

USE FOR: update Dockerfile, helm chart, bicep template, ARM template, terraform, k8s manifest, deployment config
DO NOT USE FOR: source code logic changes, test-only changes, documentation-only changes

## Instructions

### When to Apply
When modifying container images, deployment templates, or Kubernetes resource definitions.

### Step-by-Step Procedure
1. Identify the affected infrastructure files
2. For Dockerfile changes: update `otelcollector/build/linux/Dockerfile` and/or `otelcollector/build/windows/Dockerfile`
3. For Helm chart changes: coordinate with AKS RP chart (see `chart: Match AKS RP chart for values and daemonset yaml`)
4. For Bicep/ARM/Terraform: update the corresponding template directories
5. Verify multi-arch compatibility (amd64/arm64) for Dockerfile changes
6. Run `trivy fs --severity CRITICAL,HIGH .` for security impact
7. Test deployment on a dev cluster if possible

### Files Typically Involved
- `otelcollector/build/linux/Dockerfile`, `otelcollector/build/windows/Dockerfile`
- `otelcollector/build/linux/configuration-reader/Dockerfile`, `otelcollector/build/linux/ccp/Dockerfile`
- `AddonBicepTemplate/`, `AddonArmTemplate/`, `AddonTerraformTemplate/`, `AddonPolicyTemplate/`
- `ArcBicepTemplate/`, `ArcArmTemplate/`
- `.pipelines/deployment/` — EV2 deployment specs
- `otelcollector/scripts/` — container setup scripts

### Validation
- Docker image builds successfully for both amd64 and arm64
- Trivy scan passes on the built image
- Deployment templates are syntactically valid
- Helm lint passes if chart is modified

## Examples from This Repo
- `chart: Match AKS RP chart for values and daemonset yaml (#1298)`
- `fix: Correct node affinity syntax in ama-metrics DS`
- `fix: bicep fixes (#1359)`
- `Upgrade ksm for CVE fixes (#1355)`
