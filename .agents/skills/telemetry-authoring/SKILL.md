# Telemetry Authoring

## Description
Guides adding Application Insights telemetry to the prometheus-collector agent following existing patterns — SDK usage, naming conventions, and standard dimensions.

USE FOR: add telemetry, add metrics, add tracing, add observability, instrument code, track event, emit metric, add logging, add Application Insights, telemetry gap, missing telemetry
DO NOT USE FOR: fixing broken telemetry pipelines, configuring telemetry infrastructure (Fluent Bit, Log Analytics), dashboard creation, alert rule authoring

## Instructions

### When to Apply
When adding telemetry to new or existing code paths in the prometheus-collector agent.

### Step-by-Step Procedure

#### 1. Telemetry Pattern Discovery
Before adding ANY telemetry, identify the existing pattern:

- **SDK**: `github.com/microsoft/ApplicationInsights-Go/appinsights` (Go)
- **Client**: `TelemetryClient` singleton declared in `otelcollector/fluent-bit/src/telemetry.go`
- **Setup**: `shared.SetupTelemetry(customEnvironment)` in `otelcollector/shared/telemetry.go`
- **Common properties**: `CommonProperties` map — includes `computer`, `controllerType`, `cluster`, `aksRegion`, `agentVersion`
- **Gating**: Check `TELEMETRY_DISABLED` env var — if `"true"`, skip telemetry initialization

Sample files WITH telemetry:
- `otelcollector/fluent-bit/src/out_appinsights.go` — plugin lifecycle telemetry
- `otelcollector/fluent-bit/src/prometheus_collector_health.go` — health metrics
- `otelcollector/fluent-bit/src/process_stats.go` — CPU/memory telemetry
- `otelcollector/shared/health_metrics.go` — health metric emission

#### 2. What to Instrument

**Error paths (highest priority)**:
- Every `if err != nil` that represents an unexpected failure
- Include: error message, source component, operation context
- Pattern: `TelemetryClient.TrackException(...)` or log with error context

**Entry points and lifecycle**:
- Startup initialization (plugin init, process start)
- Health check endpoints and heartbeats
- Pattern: Track duration, success/failure, version info

**External calls**:
- MetricsExtension communication, Kubernetes API calls
- Pattern: Track target, duration, response status

**Critical business logic**:
- Config processing results (valid/invalid, regex matches)
- Metric collection stats (processed count, sent count)
- Pattern: `TelemetryClient.TrackMetric(...)` with `CommonProperties`

#### 3. Telemetry Conventions
- **Metric naming**: Descriptive variable names (e.g., `InvalidCustomPrometheusConfig`, `KubeletKeepListRegex`)
- **Event naming**: Action-based (e.g., `FLBPluginInit`, `SendCoreCountToAppInsightsMetrics`)
- **Standard dimensions**: Include `CommonProperties` map on all telemetry calls (computer, controllerType, cluster, aksRegion)
- **Error telemetry**: Include error message, source component prefix, operation context

#### 4. Anti-Patterns to Avoid
- Do NOT hardcode instrumentation keys — use `APPLICATIONINSIGHTS_AUTH_*` env vars decoded via `shared.SetupTelemetry()`
- Do NOT create new `TelemetryClient` instances — reuse the singleton
- Do NOT emit telemetry inside tight loops (aggregate first)
- Do NOT log secrets, tokens, or PII in telemetry properties
- Do NOT add telemetry in unit test code paths — respect `TELEMETRY_DISABLED` guard
- Do NOT use `fmt.Println` for production telemetry — use `TelemetryClient` or `log.Println`

#### 5. Validation
- Verify the `appinsights` import matches existing files
- Verify metric/event names follow the repo's naming convention
- Verify `CommonProperties` are included in telemetry calls
- Run existing unit tests to ensure no test isolation issues
- Check `TELEMETRY_DISABLED` guard is respected

## References
- `otelcollector/fluent-bit/src/telemetry.go` — telemetry client and properties
- `otelcollector/shared/telemetry.go` — telemetry setup function
- `otelcollector/fluent-bit/src/prometheus_collector_health.go` — health metric patterns
