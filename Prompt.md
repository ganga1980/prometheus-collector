# Prometheus Collector

Azure Monitor managed service for Prometheus — an agent-based solution for collecting Prometheus metrics from Kubernetes clusters and sending them to Azure Monitor.

## Tech Stack

| Component | Technology |
|-----------|------------|
| Primary Language | Go 1.24+ |
| Collector Framework | OpenTelemetry Collector v0.144.0 |
| Metrics Pipeline | Prometheus receiver → MetricsExtension → Azure Monitor |
| Telemetry Plugin | Fluent Bit (Go C-shared plugin) → Application Insights |
| Config Validation | Custom Go binary (`prom-config-validator`) |
| Target Allocation | OpenTelemetry Target Allocator |
| Configuration Reader | Go binary with cert management |
| Container Base | CBL-Mariner (Linux), Windows Server Core |
| Helm Charts | `azure-monitor-metrics-addon`, `prometheus-collector` |
| CI/CD | Azure Pipelines (1ES template), GitHub Actions (scans) |
| Testing | Ginkgo v2 + Gomega (E2E), Go `testing` (unit), Jest (TS) |
| Vulnerability Scanning | Trivy (container + filesystem) |
| Dependency Management | Dependabot (Go modules, GitHub Actions) |
| Rules Converter Tool | TypeScript (commander, ajv, js-yaml) |
| Infrastructure Templates | ARM, Bicep, Terraform, Azure Policy |
| Deployment | EV2 (Express V2), Helm |

## Architecture Overview

The prometheus-collector agent runs as a DaemonSet and ReplicaSet in AKS clusters:

1. **OTel Collector** (`otelcollector/opentelemetry-collector-builder/`) — Custom OpenTelemetry Collector with Prometheus receiver that scrapes metrics from configured targets.
2. **Prometheus Receiver** (`otelcollector/prometheusreceiver/`) — Fork of the upstream OTel Prometheus receiver with custom metric renaming and filtering.
3. **Prom Config Validator** (`otelcollector/prom-config-validator-builder/`) — Validates user-provided Prometheus scrape configs at startup.
4. **Fluent Bit Plugin** (`otelcollector/fluent-bit/src/`) — C-shared Go plugin that sends telemetry and health metrics to Application Insights.
5. **Configuration Reader** (`otelcollector/configuration-reader-builder/`) — Reads ConfigMaps and manages certificates for the collector.
6. **Target Allocator** (`otelcollector/otel-allocator/`) — Distributes scrape targets across collector replicas.
7. **Shared Library** (`otelcollector/shared/`) — Common utilities for config, telemetry setup, health metrics, and process management.
8. **Main Entry Point** (`otelcollector/main/`) — Orchestrates startup, config processing, and process lifecycle.
9. **Helm Charts** (`otelcollector/deploy/`) — Addon and standalone charts for AKS deployment.
10. **Mixins** (`mixins/`) — Prometheus recording rules and Grafana dashboards for Kubernetes, Node, and CoreDNS metrics.

## Functional Requirements

### 1) Prometheus Metrics Collection
Scrape Prometheus metrics from Kubernetes workloads using configurable scrape targets (default and custom), supporting service monitors, pod monitors, and static configs.

### 2) Metrics Pipeline
Process scraped metrics through OTel Collector pipeline — filter, rename, and export to Azure Monitor via MetricsExtension.

### 3) Multi-Platform Support
Run on Linux (amd64, arm64) and Windows nodes. Support AKS, Arc-enabled Kubernetes, and CCP (Connected Container Platform).

### 4) Configuration Management
Accept user configuration via ConfigMaps, custom resources (ServiceMonitor, PodMonitor), and Helm values. Validate configs at startup.

### 5) Health and Telemetry
Emit agent health metrics, process telemetry, and diagnostic data to Application Insights. Expose Prometheus-format health metrics.

## Non-Functional Requirements

- **Security**: Container images scanned with Trivy for CRITICAL/HIGH CVEs. Non-root execution where possible. TLS for service communication. Secrets via environment variables and mounted volumes.
- **Observability**: Application Insights telemetry for agent health, Prometheus-format self-metrics, structured logging with component prefixes.
- **Deployment**: EV2 rollouts with canary stages. Helm chart packaging and ACR publishing. Multi-arch (amd64/arm64) container images.
- **Performance**: Efficient metric scraping with target allocation across replicas. CPU/memory telemetry for resource monitoring.

## Expected Project Files

| Path | Purpose |
|------|---------|
| `otelcollector/` | Main agent source code and build files |
| `otelcollector/build/linux/Dockerfile` | Multi-stage Linux container build |
| `otelcollector/build/windows/Dockerfile` | Windows container build |
| `otelcollector/deploy/` | Helm charts and deployment configs |
| `otelcollector/test/` | E2E test suites and cluster YAML configs |
| `otelcollector/scripts/` | Setup and configuration scripts |
| `.pipelines/` | Azure Pipelines CI/CD definitions |
| `.github/workflows/` | GitHub Actions for scanning and Helm publishing |
| `mixins/` | Prometheus recording rules and Grafana dashboards |
| `tools/az-prom-rules-converter/` | CLI tool to convert Prometheus rules |
| `internal/` | Reference apps, scripts, and internal docs |
| `Addon*Template/`, `Arc*Template/` | ARM, Bicep, Terraform, Policy deployment templates |

## Environment Variables

| Variable | Description |
|----------|-------------|
| `TELEMETRY_DISABLED` | Set to `true` to disable Application Insights telemetry |
| `CONTROLLER_TYPE` | `replicaset` or `daemonset` — determines agent behavior |
| `OS_TYPE` | `linux` or `windows` — platform-specific logic |
| `CLUSTER` | AKS cluster name |
| `AKSREGION` | Azure region of the cluster |
| `AGENT_VERSION` | Version of the prometheus-collector agent |
| `customEnvironment` | Cloud environment (`azurepubliccloud`, `azureusgovernmentcloud`, etc.) |
| `CCP_METRICS_ENABLED` | Enable Connected Container Platform metrics mode |
| `APPLICATIONINSIGHTS_AUTH_PUBLIC` | Base64-encoded App Insights key (public cloud) |
| `GOLANG_VERSION` | Go version for CI builds |

## Acceptance Criteria

- All Ginkgo E2E test suites pass on a bootstrapped AKS cluster.
- Helm lint passes for all charts (`helm lint`).
- Trivy scan reports no CRITICAL/HIGH vulnerabilities.
- Azure Pipeline build completes successfully (Linux + Windows images).
- PR checklist in `.github/pull_request_template.md` is complete.
- Conventional Commits format followed.
