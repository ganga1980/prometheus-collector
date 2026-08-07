# Feature Development

## Description
Scaffolding and workflow for adding new features to the prometheus-collector.

USE FOR: add feature, implement, new endpoint, new component, new scrape target, new exporter support, add cloud support
DO NOT USE FOR: bug fixes, refactoring, documentation-only changes

## Instructions

### When to Apply
When adding new capabilities like scrape targets, cloud support, metric exporters, or operator features (44 commits/year, 16.2%).

### Step-by-Step Procedure
1. **Plan** — Identify all files that need changes. New features typically touch:
   - Source code (Go modules)
   - Configuration (ConfigMaps, Helm values)
   - Deployment manifests (Helm templates)
   - Tests (Ginkgo E2E suites)
2. **Implement core logic** — Add Go code in the appropriate module.
3. **Add configuration** — Add default scrape configs in `otelcollector/configmapparser/default-prom-configs/` if adding a new scrape target.
4. **Update Helm charts** — Add templates, values, and conditionals in both `addon-chart/` and `chart/prometheus-collector/` if applicable.
5. **Add tests** — Create Ginkgo test entries with appropriate labels.
6. **Update documentation** — Update `RELEASENOTES.md` for user-visible changes.
7. **Update PR template** — Add new test labels if applicable.

### Files Typically Involved
- `otelcollector/main/main.go` or relevant service Go files
- `otelcollector/configmapparser/default-prom-configs/*.yml`
- `otelcollector/deploy/addon-chart/azure-monitor-metrics-addon/templates/`
- `otelcollector/deploy/addon-chart/azure-monitor-metrics-addon/values.yaml`
- `otelcollector/test/ginkgo-e2e/` (new test entries)
- `RELEASENOTES.md`

### Validation
- All Go modules build successfully
- New Ginkgo tests pass with appropriate labels
- Helm charts lint cleanly
- PR template checklist completed (telemetry, docs, scale test)

## Examples from This Repo
- `feat: Add OperationEnvironment argument to MetricsExtension command execution (#1403)`
- `feat: add support for Container Storage's storage-operator (#1224)`
- `Add DCGM exporter support for GPU metrics collection (#1391)`
- `feat: Scrape metrics for node autoprovisioning (aks control plane) (#1169)`

## References
- `.github/pull_request_template.md` — New feature checklist
- `otelcollector/deploy/addon-chart/` — Helm chart templates
