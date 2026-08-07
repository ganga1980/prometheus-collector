# Prometheus Collector

Azure Monitor's OpenTelemetry-based Prometheus metrics collector for AKS and Arc-enabled Kubernetes clusters. Collects Prometheus metrics from cluster workloads and forwards them to Azure Monitor Workspace via OTLP, with telemetry health reporting to Application Insights.

## Tech Stack

| Component | Technology |
|-----------|------------|
| Core Collector | OpenTelemetry Collector v0.144.0 (custom distribution) |
| Language | Go 1.24+ |
| Telemetry Plugin | Fluent Bit (Go CGO shared library) |
| Telemetry Backend | Application Insights (Go SDK) |
| Target Allocation | OpenTelemetry Target Allocator |
| Config Validation | Prometheus config validator |
| Metrics UI | Custom Prometheus UI wrapper |
| CLI Tool | TypeScript (az-prom-rules-converter) |
| Testing | Ginkgo v2 + Gomega (E2E), Jest (TypeScript) |
| Container Runtime | Docker (multi-arch: amd64, arm64) |
| Orchestration | Kubernetes (DaemonSet + ReplicaSet) |
| IaC | Bicep, ARM Templates, Terraform, Helm Charts |
| CI/CD | GitHub Actions, Azure Pipelines |
| Security Scanning | Trivy, Dependabot |

## Architecture Overview

The collector runs as a DaemonSet in Kubernetes clusters, scraping Prometheus metrics from configured targets (kube-state-metrics, node-exporter, custom workloads). A ReplicaSet handles target allocation and the Prometheus UI. The Fluent Bit plugin sends operational telemetry to Application Insights. Configuration is managed through ConfigMaps and custom resources.

Key modules under `otelcollector/`:
- `opentelemetry-collector-builder/` — Custom OTel Collector binary
- `prometheusreceiver/` — Custom Prometheus receiver
- `fluent-bit/src/` — Application Insights Fluent Bit output plugin
- `otel-allocator/` — Target allocator for sharding scrape targets
- `prom-config-validator-builder/` — Prometheus config validation tool
- `configuration-reader-builder/` — Configuration reader with cert management
- `prometheus-ui/` — Custom Prometheus UI
- `shared/` — Shared utilities (telemetry, process management)

## Functional Requirements
### 1) Prometheus Metric Collection
Scrape Prometheus metrics from Kubernetes workloads, kube-state-metrics, node-exporter, and custom targets configured via ConfigMaps or custom resources.

### 2) Azure Monitor Integration
Forward collected metrics to Azure Monitor Workspace via OTLP protocol.

### 3) Multi-Cluster Support
Support AKS clusters, Arc-enabled Kubernetes clusters, and OpenShift clusters with appropriate authentication (managed identity, MSI, FIC).

### 4) Configuration Management
Support Prometheus scrape config via ConfigMaps (v1 and v2), custom resources, and built-in default scrape targets.

## Non-Functional Requirements
- Multi-architecture support (amd64, arm64)
- Windows and Linux node support
- Security hardening (PIE binaries, non-root containers, Trivy scanning)
- Operational telemetry via Application Insights
- Minimal ingestion profiles for cost optimization

## Expected Project Files

| Path | Purpose |
|------|---------|
| `otelcollector/opentelemetry-collector-builder/` | Main collector binary source |
| `otelcollector/fluent-bit/src/` | Fluent Bit telemetry plugin |
| `otelcollector/build/linux/Dockerfile` | Linux container image build |
| `otelcollector/build/windows/Dockerfile` | Windows container image build |
| `otelcollector/test/ginkgo-e2e/` | End-to-end test suites |
| `tools/az-prom-rules-converter/` | Prometheus rules to Azure format converter |
| `mixins/` | Prometheus recording rule mixins |
| `AddonBicepTemplate/` | Bicep deployment templates |
| `AddonTerraformTemplate/` | Terraform deployment templates |

## Environment Variables

| Variable | Purpose |
|----------|---------|
| `APPLICATIONINSIGHTS_AUTH_PUBLIC` | Base64-encoded App Insights key (Azure Public Cloud) |
| `APPLICATIONINSIGHTS_AUTH_USGOVERNMENT` | Base64-encoded App Insights key (US Government Cloud) |
| `APPLICATIONINSIGHTS_AUTH_CHINACLOUD` | Base64-encoded App Insights key (China Cloud) |
| `ACS_RESOURCE_NAME` | Resource name for non-AKS clusters |
| `CONTAINER_RUNTIME` | Container runtime name |
| `GOLANG_VERSION` | Go version for Docker builds |
| `FLUENTBIT_GOLANG_VERSION` | Go version for Fluent Bit builds |

## Acceptance Criteria
- `make all` builds successfully in `otelcollector/opentelemetry-collector-builder/`
- All Ginkgo E2E test suites pass on a bootstrapped cluster
- `npm test` passes for `tools/az-prom-rules-converter`
- `trivy fs --severity CRITICAL,HIGH .` reports no new unaccepted vulnerabilities
- Conventional Commits format used for commit messages
- PR template checklist completed
