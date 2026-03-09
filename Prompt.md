# Azure Monitor Prometheus Collector

Azure Monitor managed service for Prometheus — collects Prometheus metrics from Kubernetes clusters (AKS, Arc-enabled) and sends them to Azure Monitor. Built on OpenTelemetry Collector with custom Prometheus receiver.

## Tech Stack

| Component | Technology |
|-----------|------------|
| Core Runtime | Go 1.23+ |
| Collector Framework | OpenTelemetry Collector v0.144.0 |
| Metrics | Prometheus client_golang, OpenTelemetry SDK |
| Kubernetes | client-go, controller-runtime, Helm 3 |
| Logging | fluent-bit (Go plugin), Application Insights |
| IaC | Azure Bicep, Terraform, ARM templates |
| CI/CD | GitHub Actions, Azure Pipelines |
| Testing | Ginkgo v2, Gomega, Jest |
| Container | Docker (multi-stage, multi-arch) |
| Tooling | TypeScript (rules converter) |

## Architecture Overview

The collector runs as multiple Kubernetes workloads: a central Deployment (`ama-metrics`) for cluster-level metrics, a DaemonSet (`ama-metrics-node`) for per-node metrics, a Target Allocator for workload distribution, and supporting services (kube-state-metrics, node-exporter). Metrics are remote-written to Azure Monitor. Configuration is managed via Helm charts and ConfigMaps.

## Functional Requirements

### 1) Collect Prometheus metrics from Kubernetes clusters
Scrape metrics from kubelet, kube-state-metrics, node-exporter, and user-defined targets.

### 2) Support custom scrape configurations
Allow users to define custom scrape targets via ConfigMaps and Prometheus Operator CRDs (ServiceMonitor, PodMonitor).

### 3) Remote write to Azure Monitor
Send collected metrics to Azure Monitor managed Prometheus workspace.

### 4) Multi-platform support
Run on Linux (amd64, arm64) and Windows (ltsc2019, ltsc2022) Kubernetes nodes.

### 5) Support AKS and Arc-enabled clusters
Deploy as AKS addon or Arc extension with appropriate ARM/Bicep/Terraform templates.

## Non-Functional Requirements

- **Reliability:** Graceful shutdown on SIGTERM, inotify-based config reload
- **Security:** Non-root containers, Trivy scanning, CVE remediation, FIPS compliance
- **Observability:** Self-monitoring via Application Insights, Prometheus self-metrics
- **Scalability:** Target allocator distributes scrape load across replicas

## Expected Project Files

| Path | Purpose |
|------|---------|
| `otelcollector/main/main.go` | Main collector entry point |
| `otelcollector/otel-allocator/main.go` | Target allocator service |
| `otelcollector/deploy/addon-chart/` | AKS addon Helm chart |
| `otelcollector/deploy/chart/prometheus-collector/` | Standalone Helm chart |
| `otelcollector/configmapparser/` | Config parsing and default scrape configs |
| `otelcollector/build/linux/Dockerfile` | Multi-stage Linux container image |
| `AddonBicepTemplate/` | Azure Bicep deployment templates |
| `AddonTerraformTemplate/` | Terraform deployment templates |

## Environment Variables

| Variable | Purpose |
|----------|---------|
| `CLUSTER` | Kubernetes cluster identifier |
| `MODE` | Collector mode (simple/advanced) |
| `CONTROLLER_TYPE` | DaemonSet or ReplicaSet |
| `customEnvironment` | Cloud environment (AzurePublicCloud, etc.) |
| `APPLICATIONINSIGHTS_AUTH_*` | App Insights auth per cloud (env-specific) |
| `OTEL_ENDPOINT` | OTLP exporter endpoint |

## Acceptance Criteria

- All Go modules build successfully (`make` in each module directory)
- E2E Ginkgo tests pass for affected test labels
- Helm charts lint successfully (`helm lint`)
- Trivy scan passes with no new critical/high CVEs
- TypeScript rules converter tests pass (`npm test`)
- No hardcoded secrets or credentials in code
