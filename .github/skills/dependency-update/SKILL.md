# Dependency Update

## Description
Guides safe dependency updates across the repo's 24 Go modules, TypeScript tool, and GitHub Actions.

USE FOR: update dependency, bump package, upgrade library, renovate, dependabot, update go.mod, update package.json
DO NOT USE FOR: adding a brand new dependency, removing a dependency, major OTel Collector version migration (use `internal/otel-upgrade-scripts/upgrade.sh` instead)

## Instructions

### When to Apply
When updating Go module dependencies, npm packages, or GitHub Actions versions. Dependabot handles most routine updates automatically via `.github/dependabot.yml`.

### Step-by-Step Procedure
1. Identify which `go.mod` file(s) need updating. The repo has 24 Go modules — check if the dependency appears in multiple modules using: `grep -r "dependency-name" --include="go.mod"`
2. Update the dependency in each affected `go.mod`: `go get <package>@<version>`
3. Run `go mod tidy` in each affected module directory
4. Build to verify: `cd otelcollector/opentelemetry-collector-builder && make all`
5. For TypeScript: `cd tools/az-prom-rules-converter && npm update <package> && npm test`
6. Run `trivy fs --severity CRITICAL,HIGH .` to check for new vulnerabilities

### Files Typically Involved
- `otelcollector/*/go.mod`, `otelcollector/*/go.sum`
- `otelcollector/test/ginkgo-e2e/*/go.mod`
- `tools/az-prom-rules-converter/package.json`, `package-lock.json`
- `.github/dependabot.yml` (for Dependabot config changes)

### Validation
- `make all` succeeds in `otelcollector/opentelemetry-collector-builder/`
- `npm test` passes in `tools/az-prom-rules-converter/`
- No new critical/high Trivy findings

## Examples from This Repo
- `build(deps): bump k8s.io/client-go from 0.34.2 to 0.35.1 in /otelcollector/fluent-bit/src (#1413)`
- `build(deps): bump ajv from 8.11.2 to 8.18.0 in /tools/az-prom-rules-converter (#1416)`
- `build(deps): Upgrade otelcollector to v0.144.0 (#1401)`

## References
- `.github/dependabot.yml` — Dependabot configuration
- `internal/otel-upgrade-scripts/upgrade.sh` — OTel Collector version upgrade automation
