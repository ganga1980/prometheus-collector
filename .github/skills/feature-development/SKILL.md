# Feature Development

## Description
Guides adding new features to the prometheus-collector, including new exporters, cloud integrations, and collector capabilities.

USE FOR: add feature, implement, new endpoint, new component, new module, create, new exporter
DO NOT USE FOR: bug fixes, refactoring, documentation-only changes

## Instructions

### When to Apply
When adding new collector capabilities, cloud integrations (e.g., new Azure clouds like Bleu), new exporters (e.g., DCGM), or new configuration options.

### Step-by-Step Procedure
1. **Determine the component.** New features typically land in `otelcollector/` — identify which sub-component (receiver, allocator, config, main).
2. **Create source files** following Go naming conventions. Place in the appropriate package directory.
3. **Update configuration** if the feature requires new settings:
   - Add environment variable handling in `main/main.go` or relevant component.
   - Update ConfigMap parsing in `configmapparser/` if needed.
   - Add Helm chart values in `deploy/chart/prometheus-collector/`.
4. **Add telemetry.** New features should include Application Insights tracking for errors and key operations (see `fluent-bit/src/telemetry.go` for patterns).
5. **Write tests:**
   - Go unit tests (`*_test.go`) for logic.
   - Ginkgo E2E test in `otelcollector/test/ginkgo-e2e/` for integration behavior.
6. **Update documentation:**
   - Add to `RELEASENOTES.md`.
   - Update `otelcollector/test/README.md` if new test labels are needed.
7. **Commit** with format: `feat: <description>`

### Files Typically Involved
- `otelcollector/main/main.go` — feature flags and initialization
- `otelcollector/prometheusreceiver/` — receiver enhancements
- `otelcollector/shared/` — shared utilities
- `otelcollector/deploy/chart/` — Helm chart updates
- `otelcollector/test/ginkgo-e2e/` — E2E tests

### Validation
- `go build ./...` succeeds
- `go test ./...` passes
- Ginkgo E2E tests pass for affected labels
- PR template filled with feature checklist (telemetry, docs, perf testing)

## Examples from This Repo
- `b98f324` — feat: add DCGM exporter support
- `60fd3dc` — feat: add Bleu cloud support
- `a7b39e4` — feat: EV2 deployment integration
