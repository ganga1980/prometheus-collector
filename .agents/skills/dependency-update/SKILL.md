# Dependency Update

## Description
Guides safe updates of Go modules, npm packages, and GitHub Actions across the multiple module directories in this repository.

USE FOR: update dependency, bump package, upgrade library, renovate, dependabot, update go.mod, update package.json
DO NOT USE FOR: adding a brand new dependency, removing a dependency, major version migration of the OTel Collector

## Instructions

### When to Apply
When updating package versions in any of the Go modules or the TypeScript tool. Most common for Dependabot-initiated bumps and OTel Collector upgrades.

### Step-by-Step Procedure
1. Identify which `go.mod` file(s) need updating. This repo has modules at:
   - `otelcollector/opentelemetry-collector-builder/go.mod`
   - `otelcollector/prom-config-validator-builder/go.mod`
   - `otelcollector/fluent-bit/src/go.mod`
   - `otelcollector/prometheusreceiver/go.mod`
   - `otelcollector/otel-allocator/go.mod`
   - `otelcollector/prometheus-ui/go.mod`
   - `otelcollector/configuration-reader-builder/go.mod`
   - `otelcollector/shared/go.mod`
   - `otelcollector/go.mod`
   - `internal/referenceapp/golang/go.mod`
   - `otelcollector/test/ginkgo-e2e/*/go.mod`
2. Update the dependency: `go get <package>@<version>` in the relevant directory.
3. Run `go mod tidy` in each affected module directory.
4. Verify no other modules reference the old version: `grep -r "<package>" --include="go.mod"`.
5. For npm updates: `cd tools/az-prom-rules-converter && npm update <package> && npm install`.
6. Build affected components to verify compatibility (e.g., `cd otelcollector/opentelemetry-collector-builder && make`).
7. Run existing tests to verify nothing broke.

### Files Typically Involved
- `otelcollector/*/go.mod`, `otelcollector/*/go.sum`
- `tools/az-prom-rules-converter/package.json`, `package-lock.json`
- `.github/dependabot.yml` (for adding new module paths)

### Validation
- `go build ./...` succeeds in each affected module
- `go mod tidy` produces no changes
- `helm lint` passes for charts
- Existing E2E tests pass

## Examples from This Repo
- `build(deps): bump k8s.io/client-go from 0.34.2 to 0.35.1 in /otelcollector/fluent-bit/src (#1413)`
- `build(deps): Upgrade otelcollector to v0.144.0 (#1401)`
- `build(deps): bump ajv from 8.11.2 to 8.18.0 in /tools/az-prom-rules-converter (#1416)`

## References
- `.github/dependabot.yml` — configured module paths and schedules
- `OPENTELEMETRY_VERSION` — current OTel Collector version
