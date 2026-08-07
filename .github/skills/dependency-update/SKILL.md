# Dependency Update

## Description
Guides safe dependency updates for Go modules, npm packages, and container base images in this repository.

USE FOR: update dependency, bump package, upgrade library, renovate, dependabot, update go.mod, update package.json
DO NOT USE FOR: adding a brand new dependency, removing a dependency, major OTel version migration (use otelcollector-upgrade.yml workflow instead)

## Instructions

### When to Apply
When updating package versions in any of the 24 Go modules, the npm package, or Docker base images. This is the most common commit pattern (129 commits/year, 47.6%).

### Step-by-Step Procedure
1. Identify which module(s) need updating — check the `go.mod` file in the relevant directory.
2. Update the dependency: `go get <package>@<version>` in the correct module directory.
3. Run `go mod tidy` to clean up.
4. If multiple `go.mod` files reference the same dependency, update ALL of them:
   - `otelcollector/opentelemetry-collector-builder/go.mod`
   - `otelcollector/otel-allocator/go.mod`
   - `otelcollector/shared/go.mod`
   - `otelcollector/fluent-bit/src/go.mod`
   - `otelcollector/prometheusreceiver/go.mod`
   - `internal/referenceapp/golang/go.mod`
   - E2E test modules under `otelcollector/test/ginkgo-e2e/*/go.mod`
5. Build the affected module: `make` (or `go build ./...`).
6. Run tests: `go test ./...` in the affected module.
7. For npm: `cd tools/az-prom-rules-converter && npm update <package> && npm test`.

### Files Typically Involved
- `otelcollector/*/go.mod`, `otelcollector/*/go.sum`
- `internal/referenceapp/golang/go.mod`
- `tools/az-prom-rules-converter/package.json`, `package-lock.json`
- `otelcollector/build/linux/Dockerfile` (base image updates)

### Validation
- `go build ./...` succeeds in affected modules
- `go test ./...` passes
- `go mod verify` shows no issues
- No new critical/high Trivy findings

## Examples from This Repo
- `build(deps): bump k8s.io/client-go from 0.34.2 to 0.35.1 in /otelcollector/fluent-bit/src (#1413)`
- `build(deps): Upgrade otelcollector to v0.144.0 (#1401)`
- `build(deps): bump ajv from 8.11.2 to 8.18.0 in /tools/az-prom-rules-converter (#1416)`

## References
- `.github/dependabot.yml` — Dependabot configuration (daily updates, 2 PRs per ecosystem)
- `OPENTELEMETRY_VERSION` — Pinned OTel version (do not bump via dependabot)
