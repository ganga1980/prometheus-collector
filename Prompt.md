# prometheus-collector

Azure Monitor Prometheus metrics collection system built on the OpenTelemetry Collector framework. Deploys as Kubernetes DaemonSet/ReplicaSet pods on AKS and Arc-enabled clusters to collect and forward Prometheus metrics to Azure Monitor.

## Tech Stack

| Component | Technology |
|-----------|------------|
| Core Language | Go 1.23+ |
| Collector Framework | OpenTelemetry Collector v1.50.0 |
| Metrics Library | Prometheus v0.309.2 |
| Kubernetes Client | client-go v0.34.3 |
| Azure SDK | azure-sdk-for-go (azcore, azidentity, azquery) |
| Logging Plugin | Fluent Bit (Go CGO plugin) |
| Telemetry | Application Insights + OTLP |
| CLI Tooling | TypeScript (Commander, AJV) |
| Monitoring Mixins | Jsonnet/libsonnet |
| Container Runtime | Docker (multi-stage, multi-arch) |
| Orchestration | Kubernetes + Helm |
| IaC | Bicep, Terraform, ARM Templates |
| CI/CD | GitHub Actions + Azure Pipelines |
| Testing | Ginkgo v2 + Gomega (E2E), Go testing, Jest |
| Security Scanning | Trivy, Dependabot |

## Architecture Overview

The system consists of a custom OpenTelemetry Collector with a Prometheus receiver that scrapes Kubernetes workloads. A target allocator distributes scrape targets across collector replicas. Configuration is managed via ConfigMaps with two modes: Metrics Platform (MP) and Cloud Config Platform (CCP). Fluent Bit handles Application Insights telemetry. Helm charts manage deployment across AKS addon and Arc extension modes.

## Functional Requirements

### 1) Prometheus Metric Collection
Scrape Prometheus metrics from Kubernetes pods, services, and node exporters using the custom OTel Prometheus receiver with support for PodMonitor/ServiceMonitor CRDs.

### 2) Multi-Mode Operation
Support DaemonSet mode (per-node collection) and ReplicaSet mode (centralized collection with target allocation) configurable via `MODE` environment variable.

### 3) Multi-Platform Support
Run on Linux (amd64, arm64) and Windows (ltsc2019, ltsc2022) nodes with platform-specific Dockerfiles and configurations.

### 4) Azure Monitor Integration
Forward collected metrics to Azure Monitor Workspace via OTLP/Remote Write, with support for multiple Azure clouds (Prod, Fairfax, Mooncake, USSec, USNat, Bleu).

### 5) Dynamic Configuration
Support runtime configuration via ConfigMaps, custom resource definitions (PodMonitor, ServiceMonitor), and file-based settings with inotify-based hot reload.

## Non-Functional Requirements

- **Security**: Distroless container images, PIE+RELRO binary hardening, TLS with dynamic cert rotation, Bearer token auth, Azure Managed Identity.
- **Observability**: Self-monitoring via Application Insights, Prometheus self-scraping, structured logging (slog).
- **Performance**: Multi-arch support (amd64/arm64), efficient scrape target allocation, configurable scrape intervals.
- **Reliability**: Health/liveness probes, graceful shutdown (SIGTERM handling), certificate validation at startup.

## Expected Project Files

| Path | Purpose |
|------|---------|
| `otelcollector/main/main.go` | Collector entry point |
| `otelcollector/prometheusreceiver/` | Custom Prometheus receiver |
| `otelcollector/shared/` | Shared configmap libraries |
| `otelcollector/deploy/chart/` | Main Helm chart |
| `otelcollector/build/linux/Dockerfile` | Linux container image |
| `otelcollector/test/ginkgo-e2e/` | E2E test suites |
| `mixins/` | Prometheus recording rules/dashboards |
| `tools/az-prom-rules-converter/` | TypeScript CLI tool |

## Environment Variables

| Variable | Description |
|----------|-------------|
| `MODE` | Collector mode: `advanced` or `noDefaultScrapingEnabled` |
| `CLUSTER` | Kubernetes cluster name |
| `AKSREGION` | AKS cluster region |
| `customEnvironment` | Azure cloud environment |
| `AZMON_OPERATOR_HTTPS_ENABLED` | Enable HTTPS for target allocator |
| `APPLICATIONINSIGHTS_AUTH` | AppInsights instrumentation key (base64) |
| `TELEMETRY_APPLICATIONINSIGHTS_KEY` | Alternative AppInsights key |
| `CONTROLLER_TYPE` | DaemonSet or ReplicaSet |
| `OS_TYPE` | linux or windows |

## Acceptance Criteria

- All Ginkgo E2E tests pass on a bootstrapped cluster.
- Go unit tests pass: `cd otelcollector && go test ./...`
- Trivy scan reports no new critical/high vulnerabilities.
- Docker image builds successfully for linux/amd64 and linux/arm64.
- PR template checklist completed (new features: telemetry, docs, perf testing).
