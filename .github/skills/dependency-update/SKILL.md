# Dependency Update

## Description
Guides safe dependency updates in this multi-module Go repository with 24 `go.mod` files, plus npm dependencies in the TypeScript CLI tool.

USE FOR: update dependency, bump package, upgrade library, renovate, dependabot, update go.mod, update package.json
DO NOT USE FOR: adding a brand new dependency, removing a dependency, major version migration requiring API changes

## Instructions

### When to Apply
When updating Go modules, npm packages, or Helm chart dependencies. This is the most frequent change pattern (~90 commits/year, 34% of all commits), largely automated by Dependabot.

### Step-by-Step Procedure
1. **Identify which modules are affected.** Run `find . -name 'go.mod' | head -30` to see all Go module locations. The primary module is `otelcollector/go.mod`.
2. **Update the dependency** in the relevant `go.mod`:
   - For Go: `cd otelcollector && go get <package>@<version>`
   - For npm: `cd tools/az-prom-rules-converter && npm install <package>@<version>`
3. **Run `go mod tidy`** in every affected module directory. The primary module has `replace` directives for `shared/` — these must be preserved.
4. **Check for transitive impacts.** If updating an OpenTelemetry or Prometheus dependency, verify that `otel-allocator/`, `prometheusreceiver/`, and `fluent-bit/src/` modules are compatible.
5. **Build all affected components:** `cd otelcollector && go build ./...`
6. **Run tests:** `cd otelcollector && go test ./...`
7. **Commit lockfiles.** Always commit `go.sum` changes alongside `go.mod`.

### Files Typically Involved
- `otelcollector/go.mod`, `otelcollector/go.sum`
- `otelcollector/otel-allocator/go.mod`
- `otelcollector/prometheusreceiver/go.mod`
- `otelcollector/fluent-bit/src/go.mod`
- `tools/az-prom-rules-converter/package.json`, `package-lock.json`

### Validation
- `go build ./...` succeeds in affected modules
- `go test ./...` passes
- `npm test` passes (if TypeScript changes)
- No new Trivy critical/high vulnerabilities introduced

## Examples from This Repo
- `701eb75` — build(deps): bump go.opentelemetry.io/collector
- `00b142f` — build(deps): bump github.com/prometheus/prometheus
- `49c9c8e` — build(deps): bump k8s.io/client-go
