# Repository Instructions

## Summary

This is the **Azure Monitor managed service for Prometheus** (`prometheus-collector`). It is the agent-based solution for collecting Prometheus metrics from Kubernetes clusters and sending them to Azure Monitor. Primary language is **Go** (~78%), with Shell scripts, TypeScript tooling, and Python reference apps. Built on the **OpenTelemetry Collector** framework with custom Prometheus receiver, Fluent Bit telemetry plugin, MetricsExtension integration, and Helm chart packaging. Runs on **Linux** (amd64/arm64) and **Windows** containers in AKS.

## General Guidelines

1. Follow Conventional Commits format (`feat:`, `fix:`, `build(deps):`, `test:`, `docs:`).
2. Run Ginkgo E2E tests on a bootstrapped cluster before merging — see `otelcollector/test/README.md`.
3. Fill out the PR checklist in `.github/pull_request_template.md` for every PR.
4. If newer commits make prior changes unnecessary, revert them.
5. Never commit secrets, instrumentation keys, or connection strings — use environment variables.
6. Load `.github/instructions/go.instructions.md` for Go code conventions.
7. Load `.github/instructions/shell.instructions.md` for shell script conventions.

## Build Instructions

### Prerequisites
- Go 1.24+ (see `GOLANG_VERSION` in `.pipelines/azure-pipeline-build.yml`)
- Docker with multi-arch buildx support
- Helm 3.12+
- Node.js (for `tools/az-prom-rules-converter`)

### Build Components
```bash
# Build OTel Collector
cd otelcollector/opentelemetry-collector-builder && make

# Build Prometheus config validator
cd otelcollector/prom-config-validator-builder && make

# Build Fluent Bit plugin
cd otelcollector/fluent-bit/src && make

# Build Prometheus UI
cd otelcollector/prometheus-ui && make

# Build configuration reader
cd otelcollector/configuration-reader-builder && make

# Build target allocator
cd otelcollector/otel-allocator && make

# Build Linux container image
cd otelcollector && docker buildx build -f build/linux/Dockerfile .

# Build rules converter tool
cd tools/az-prom-rules-converter && npm install && npm run build
```

### Run Tests
```bash
# Ginkgo E2E tests (requires bootstrapped AKS cluster)
cd otelcollector/test/ginkgo-e2e/<suite> && go test -v ./...

# TypeScript tool tests
cd tools/az-prom-rules-converter && npm test

# Go unit tests
cd otelcollector/prometheusreceiver && go test ./...
```

### Lint
```bash
# Helm lint
helm lint otelcollector/deploy/addon-chart/azure-monitor-metrics-addon/
helm lint otelcollector/deploy/chart/prometheus-collector/
```

## Task-Specific Skills

| Skill | Triggers | Description |
|-------|----------|-------------|
| `dependency-update` | update dependency, bump package, dependabot | Safe Go module and npm dependency updates |
| `test-authoring` | add test, write test, test coverage | Write Ginkgo E2E or Go unit tests |
| `bug-fix` | fix bug, resolve issue, hotfix | Structured bug-fix with regression test |
| `feature-development` | add feature, implement, new endpoint | New feature scaffolding |
| `ci-cd-pipeline` | update pipeline, CI change, workflow | Modify Azure Pipelines or GitHub Actions |
| `security-review` | security review, STRIDE, credential check | STRIDE-based security review |
| `telemetry-authoring` | add telemetry, add metrics, instrument | Add Application Insights telemetry |
| `fix-critical-vulnerabilities` | fix CVE, trivy fix, vulnerability | Fix critical/high CVEs using Trivy |

## Known Patterns & Gotchas

- The OTel Collector build uses `replace` directives in `go.mod` for local packages (`shared`, `prometheusreceiver`).
- Fluent Bit plugin is built as a C shared library (`-buildmode=c-shared`) — requires CGO.
- Windows builds use a separate Dockerfile at `otelcollector/build/windows/Dockerfile`.
- The `TELEMETRY_DISABLED=true` env var disables all Application Insights telemetry.
- Dependabot is configured for multiple `go.mod` locations — see `.github/dependabot.yml`.
- The OTel Collector version is tracked in `OPENTELEMETRY_VERSION` at the repo root.
- Helm chart generation uses `otelcollector/deploy/addon-chart/generate_helm_files.sh`.
- CI builds via Azure Pipelines (`.pipelines/azure-pipeline-build.yml`) — not GitHub Actions.
