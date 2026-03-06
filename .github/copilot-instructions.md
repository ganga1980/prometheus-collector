# Repository Instructions

## Summary

This is **prometheus-collector**, an Azure Monitor Prometheus metrics collection system built on the OpenTelemetry Collector framework. Primary languages are **Go** (~17%) and **YAML/Jsonnet** (~34% for configs and monitoring mixins), with TypeScript for CLI tooling. It runs as a Kubernetes DaemonSet/ReplicaSet collecting Prometheus metrics from AKS and Arc-enabled clusters, forwarding them to Azure Monitor.

## General Guidelines

1. Follow Conventional Commits format: `feat:`, `fix:`, `docs:`, `test:`, `build:`, `ci:`, `refactor:`.
2. All Go code uses `camelCase` for variables/functions, `UPPERCASE` for constants, and `fmt.Errorf("...: %w", err)` for error wrapping.
3. Never hardcode secrets — use environment variables or mounted credential files.
4. Before submitting PRs, fill the PR template at `.github/pull_request_template.md` including the Ginkgo E2E test checklist.
5. If newer commits make prior changes unnecessary, revert them.

## Architecture Overview

```
otelcollector/                     # Core collector (Go)
├── main/                          # Entry point (DaemonSet/ReplicaSet modes)
├── prometheusreceiver/            # Custom OTel Prometheus receiver
├── opentelemetry-collector-builder/ # Custom OTel collector build
├── configuration-reader-builder/  # Config parsing + cert management
├── fluent-bit/                    # Fluent Bit AppInsights plugin (Go CGO)
├── otel-allocator/                # Target allocation service
├── prometheus-ui/                 # Custom Prometheus UI
├── prom-config-validator-builder/ # Config validation tool
├── shared/                        # Shared Go libraries (configmap/mp, configmap/ccp)
├── configmapparser/               # ConfigMap parsing utilities
├── deploy/                        # Helm charts + K8s manifests
│   ├── chart/prometheus-collector/
│   ├── addon-chart/
│   └── dependentcharts/           # node-exporter, kube-state-metrics
├── build/                         # Dockerfiles (linux/, windows/)
└── test/                          # Ginkgo E2E tests (8 test suites)
internal/                          # Reference apps, monitoring, upgrade scripts
mixins/                            # Prometheus recording rules & dashboards (Jsonnet)
tools/az-prom-rules-converter/     # TypeScript CLI for rule conversion
```

## Build Instructions

**Go collector (main):**
```bash
cd otelcollector && go build ./...
```

**OpenTelemetry Collector custom build:**
```bash
cd otelcollector/opentelemetry-collector-builder && go build -o otelcollector .
```

**TypeScript rules converter:**
```bash
cd tools/az-prom-rules-converter && npm install && npm run build && npm test
```

**Prometheus mixins:**
```bash
cd mixins/kubernetes && make
```

**Docker images (Linux):**
```bash
docker build -f otelcollector/build/linux/Dockerfile .
```

## Testing

**Ginkgo E2E tests** (require a bootstrapped K8s cluster — see `otelcollector/test/README.md`):
```bash
cd otelcollector/test/ginkgo-e2e/<suite> && go test -v ./...
```

Test suites use labels: `operator`, `windows`, `arm64`, `arc-extension`, `fips`.

**Go unit tests:**
```bash
cd otelcollector && go test ./...
```

**TypeScript tests:**
```bash
cd tools/az-prom-rules-converter && npm test
```

## Task-Specific Skills

| Skill | Triggers | Description |
|-------|----------|-------------|
| `#dependency-update` | update dependency, bump package | Safe dependency updates with Go mod tidy and testing |
| `#bug-fix` | fix bug, resolve issue, hotfix | Structured bug fix with regression test |
| `#feature-development` | add feature, implement, new endpoint | New feature scaffolding with test and doc requirements |
| `#test-authoring` | add test, write test | Create Ginkgo E2E or Go unit tests following conventions |
| `#documentation` | update docs, write readme | Documentation following repo conventions |
| `#code-refactoring` | refactor, restructure, rename | Refactoring with behavior preservation verification |
| `#infrastructure` | update helm, modify k8s, change bicep | IaC changes across Helm/Bicep/Terraform |
| `#security-review` | security review, STRIDE analysis | STRIDE-based security review |
| `#telemetry-authoring` | add telemetry, add metrics | Add telemetry following existing OTel/AppInsights patterns |
| `#fix-critical-vulnerabilities` | fix CVE, trivy fix | Fix critical/high vulns using Trivy scanning |

## Known Patterns & Gotchas

- The `otelcollector/` Go module uses `replace` directives for shared libraries — always run `go mod tidy` after dependency changes.
- Multiple `go.mod` files exist (24 total) — dependency updates may need to touch several modules.
- Linux Dockerfiles use multi-stage builds with `mcr.microsoft.com/azurelinux/distroless/base:3.0` — no shell available in final image.
- Build flags include `-buildmode=pie -ldflags '-linkmode external -extldflags=-Wl,-z,now'` for security hardening.
- The `shared/` module provides configmap utilities for both Metrics Platform (mp) and Cloud Config Platform (ccp) modes.
- Helm chart templates are in `otelcollector/deploy/chart/` with dependent charts (node-exporter, kube-state-metrics) in `deploy/dependentcharts/`.
