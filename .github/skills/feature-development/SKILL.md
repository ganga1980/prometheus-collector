# Feature Development

## Description
Guides adding new features to the Prometheus Collector, including new scrape targets, new exporters, configuration options, and cluster support.

USE FOR: add feature, implement, new component, new scrape target, new exporter, add support, enable feature
DO NOT USE FOR: bug fixes, refactoring, documentation-only changes

## Instructions

### When to Apply
When adding new functionality such as new scrape targets, new configuration options, new authentication methods, or new deployment features.

### Step-by-Step Procedure
1. Identify which component(s) need modification under `otelcollector/`
2. Add new Go source files in the appropriate module directory
3. Update `go.mod` if new dependencies are needed: `go get <package>`
4. Add configuration support in the configuration reader if needed
5. Update Dockerfiles (`otelcollector/build/linux/Dockerfile`, `otelcollector/build/windows/Dockerfile`) if new binaries or build stages are required
6. Add Ginkgo E2E tests: create or update a test suite under `otelcollector/test/ginkgo-e2e/`
7. Add test cluster YAML configs if new scrape jobs are needed: `otelcollector/test/test-cluster-yamls/`
8. Update deployment templates (Helm charts, Bicep, ARM, Terraform) if configuration changes affect deployment
9. Build and verify: `cd otelcollector/opentelemetry-collector-builder && make all`
10. Fill out the PR template checklist, especially the New Feature Checklist

### Files Typically Involved
- `otelcollector/opentelemetry-collector-builder/` — main collector
- `otelcollector/configuration-reader-builder/` — config reader
- `otelcollector/build/linux/Dockerfile` — container image
- `otelcollector/test/ginkgo-e2e/` — E2E tests
- `otelcollector/test/test-cluster-yamls/` — test configurations
- Deployment templates (`AddonBicepTemplate/`, `AddonArmTemplate/`, etc.)

### Validation
- `make all` succeeds
- New Ginkgo E2E tests pass
- PR template New Feature Checklist completed (telemetry, one-pager, scale testing)

## Examples from This Repo
- `feat: Add OperationEnvironment argument to MetricsExtension command execution (#1403)`
- `Enable DCGM exporter by default and optimize label handling (#1417)`
- `Add arc msi support for openshift clusters (#1310)`
