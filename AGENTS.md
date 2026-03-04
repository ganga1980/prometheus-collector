# AGENTS.md

## Setup Commands

```bash
# 1. Clone the repository
git clone https://github.com/ganga1980/prometheus-collector.git
cd prometheus-collector

# 2. Ensure Go 1.24+ is installed
go version

# 3. Download Go dependencies for main components
cd otelcollector/opentelemetry-collector-builder && go mod download && cd ../..
cd otelcollector/prom-config-validator-builder && go mod download && cd ../..
cd otelcollector/fluent-bit/src && go mod download && cd ../..
cd otelcollector/otel-allocator && go mod download && cd ../..

# 4. Build all Go components
cd otelcollector/opentelemetry-collector-builder && make && cd ../..
cd otelcollector/prom-config-validator-builder && make && cd ../..
cd otelcollector/fluent-bit/src && make && cd ../..
cd otelcollector/otel-allocator && make && cd ../..

# 5. Build TypeScript rules converter tool
cd tools/az-prom-rules-converter && npm install && npm run build && cd ../..

# 6. (Optional) Build Docker images — requires Docker with buildx
cd otelcollector && docker buildx build --build-arg GOLANG_VERSION=1.25.7 --build-arg FLUENTBIT_GOLANG_VERSION=1.25.7 --build-arg PROMETHEUS_VERSION=3.2.1 -f build/linux/Dockerfile .
```

## Code Style

### Go
- **Naming**: `camelCase` for local variables, `PascalCase` for exported symbols.
- **Error handling**: Always check errors with `if err != nil` — log and return or `log.Fatal`.
- **Imports**: Standard library first, then third-party, then local packages. Use blank identifier imports for side-effect-only packages.
- **Logging**: Use `log.Println` / `log.Printf` for informational messages, `log.Fatalf` for fatal errors. Prefix log messages with the component name (e.g., `"prom-config-validator::"`).
- **Environment variables**: Access via `os.Getenv()` or `shared.GetEnv(key, default)`. Never hardcode secrets.
- **CGO**: Required for the Fluent Bit plugin (`out_appinsights.so`). Use `CGO_ENABLED=1`.
- **Build tags**: Use `//go:build` directives for OS-specific files (e.g., `_linux.go`, `_windows.go`).

### Shell (Bash)
- Use `#!/bin/bash` shebang.
- Use `sudo` for package installation commands.
- Use `chmod` with restrictive permissions (544 for scripts, 744 for directories).
- Quote variables in conditions and arguments.

### TypeScript
- Use `tsc` for compilation, `jest` for testing.
- Follow the `commander` pattern for CLI tools.
- Use `js-yaml` for YAML parsing, `ajv` for JSON schema validation.

## Testing Instructions

### Ginkgo E2E Tests (Primary)
The primary test framework is **Ginkgo v2** with **Gomega** matchers, running against a live AKS cluster.

```bash
# Bootstrap a dev cluster first — see otelcollector/test/README.md
# Run a specific test suite:
cd otelcollector/test/ginkgo-e2e/<suite> && go test -v ./...
```

**Test suites** (in `otelcollector/test/ginkgo-e2e/`):
- `configprocessing` — Config processing validation
- `containerstatus` — Container readiness and process checks
- `livenessprobe` — Liveness probe restart behavior
- `operator` — Operator functionality (`label=operator`)
- `prometheusui` — Prometheus UI API validation
- `querymetrics` — Metric query validation against Azure Monitor workspace
- `regionTests` — Region-specific tests

**Test labels**: `operator`, `windows`, `arm64`, `arc-extension`, `fips`

### Go Unit Tests
```bash
cd otelcollector/prometheusreceiver && go test ./...
cd otelcollector/shared && go test ./...
```

### TypeScript Tests
```bash
cd tools/az-prom-rules-converter && npm test
```

### Test file naming:
- Go E2E: `*_test.go` with Ginkgo `Describe`/`It` blocks
- Go unit: `*_test.go` with standard `testing` package
- TypeScript: Jest (configured in `package.json`)

## Dev Environment Tips

- **Required env vars** for telemetry (non-secret names): `TELEMETRY_DISABLED`, `CONTROLLER_TYPE`, `OS_TYPE`, `NODE_IP`, `CLUSTER`, `AKSREGION`, `AGENT_VERSION`, `customEnvironment`
- **Disable telemetry** during development: set `TELEMETRY_DISABLED=true`
- **Multiple `go.mod` files**: This repo has Go modules at multiple paths. Run `go mod download` in each module directory before building.
- **Helm chart development**: Charts are in `otelcollector/deploy/addon-chart/` and `otelcollector/deploy/chart/`.
- **Test images**: After a PR build, images are tagged `0.0.0-{branch}-{date}-{commit}`.
- **Mixins**: The `mixins/` directory contains Prometheus recording rules and dashboards generated from jsonnet. Build with `make` in each mixin directory.

## PR Instructions

- **Commit message format**: Conventional Commits (`feat:`, `fix:`, `build(deps):`, `test:`, `docs:`, `chore:`)
- **Branch naming**: Feature branches; dependabot creates `dependabot/gomod/...` branches
- **Required checks**: Azure Pipeline build, Trivy scan, Helm lint
- **PR template**: Fill out `.github/pull_request_template.md` — includes checklists for new features and tests
- **Merge strategy**: Squash merge preferred
- **Testing requirement**: Run Ginkgo E2E tests on a bootstrapped cluster; list the labels used
