# Feature Development

## Description
Guides adding new features to the prometheus-collector agent, including proper file placement, configuration, testing, and telemetry.

USE FOR: add feature, implement, new endpoint, new component, new module, new scrape target, add support
DO NOT USE FOR: bug fixes, refactoring, documentation-only changes, dependency updates

## Instructions

### When to Apply
When implementing new functionality such as new scrape targets, new metric exporters, configuration options, or Helm chart features.

### Step-by-Step Procedure
1. **Plan**: Identify which components are affected and what files need to be added or modified.
2. **Implement** in the appropriate component directory:
   - New scrape target configs → `otelcollector/deploy/addon-chart/` (Helm values) + `otelcollector/shared/` (keep-list regexes)
   - New collector functionality → `otelcollector/opentelemetry-collector-builder/`
   - New config options → `otelcollector/prom-config-validator-builder/` + `otelcollector/configuration-reader-builder/`
   - New telemetry → `otelcollector/fluent-bit/src/`
   - Shared utilities → `otelcollector/shared/`
3. **OS support**: Add both `_linux.go` and `_windows.go` variants if the feature has platform-specific behavior.
4. **Configuration**: Update Helm chart values and templates if new config options are added.
5. **Telemetry**: Add Application Insights telemetry for new code paths using `TelemetryClient`.
6. **Tests**: Add Ginkgo E2E tests in `otelcollector/test/ginkgo-e2e/` and update test scrape configs if needed.
7. **Documentation**: Update `RELEASENOTES.md` with the new feature.
8. **Commit**: Use `feat:` prefix (e.g., `feat: Add DCGM exporter support for GPU metrics collection`).

### Files Typically Involved
- `otelcollector/shared/*.go` — keep-list regexes, environment variables
- `otelcollector/fluent-bit/src/telemetry.go` — telemetry variable declarations
- `otelcollector/deploy/addon-chart/azure-monitor-metrics-addon/` — Helm templates and values
- `otelcollector/test/test-cluster-yamls/` — test scrape job configs
- `otelcollector/test/ginkgo-e2e/querymetrics/` — metric query tests

### Validation
- All affected components build successfully
- `helm lint` passes for modified charts
- Ginkgo E2E tests pass with the new feature enabled
- Telemetry is emitted for new code paths
- PR checklist in `.github/pull_request_template.md` is complete

## Examples from This Repo
- `feat: Add OperationEnvironment argument to MetricsExtension command execution (#1403)` (b98f324)
- `Add DCGM exporter support for GPU metrics collection (#1391)` (85aa399)
- `Enable DCGM exporter by default and optimize label handling (#1417)` (ce58307)

## References
- `CONTRIBUTING.md` — contribution and test image guidelines
- `.github/pull_request_template.md` — new feature checklist
