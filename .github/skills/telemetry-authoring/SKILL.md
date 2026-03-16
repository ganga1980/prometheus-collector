# Telemetry Authoring

## Description
Guides adding telemetry instrumentation following this repo's existing Application Insights and OpenTelemetry patterns.

USE FOR: add telemetry, add metrics, add tracing, add observability, instrument code, track event, emit metric, add logging, add Application Insights
DO NOT USE FOR: fixing broken telemetry pipelines, configuring telemetry infrastructure, dashboard creation, alert rule authoring

## Instructions

### When to Apply
When adding telemetry to new or existing code paths, especially error handlers, entry points, and external service calls.

### Step-by-Step Procedure

#### 1. Telemetry Pattern Discovery
Before adding ANY telemetry, identify the existing pattern in the module:

- **Fluent Bit plugin** (`otelcollector/fluent-bit/src/`): Uses `ApplicationInsights-Go` SDK (`github.com/microsoft/ApplicationInsights-Go`). Telemetry client initialized in `appinsights.go`. Health metrics via `prometheus_collector_health.go`. Process stats via `process_stats.go`.
- **Shared module** (`otelcollector/shared/`): Uses `SetupTelemetry()` in `telemetry.go` to configure AI endpoint by cloud environment. Telemetry key from base64-encoded env vars.
- **OTel Collector**: Instrumented via OpenTelemetry SDK internally.

#### 2. What to Instrument (in priority order)

a. **Error paths** — Every `if err != nil` block that represents an unexpected failure. Include: error type, message, operation context. Use `FLBLogger.Printf` for Fluent Bit modules.

b. **Entry points** — HTTP handlers, Fluent Bit plugin entry points (`FLBPluginFlushCtx`). Track: operation name, duration, success/failure.

c. **External calls** — Outbound HTTP calls to Azure Monitor, Application Insights. Track: target, duration, status.

d. **Health signals** — Component initialization success/failure, liveness probe signals, config validation results.

#### 3. Conventions

- **Env vars for keys:** `APPLICATIONINSIGHTS_AUTH_PUBLIC`, `APPLICATIONINSIGHTS_AUTH_USGOVERNMENT`, `APPLICATIONINSIGHTS_AUTH_CHINACLOUD`
- **Key decoding:** Base64 decode with `encoding/base64.StdEncoding.DecodeString()`
- **Cloud-specific endpoints:** See `otelcollector/shared/telemetry.go` for endpoint mapping
- **Logging:** Use structured `log.Printf` with contextual info; use `FLBLogger.Printf` in Fluent Bit
- **No PII:** Never log request bodies, credentials, or user data in telemetry

#### 4. Anti-Patterns to Avoid
- Do NOT hardcode instrumentation keys — always use env vars
- Do NOT introduce new telemetry SDKs — use existing `ApplicationInsights-Go` or shared helpers
- Do NOT add telemetry inside tight loops (excessive volume)
- Do NOT use `fmt.Println` for production telemetry
- Do NOT emit telemetry in unit test code paths

### Validation
- Verify telemetry import matches existing files in the same module
- Build succeeds: `cd otelcollector/opentelemetry-collector-builder && make all`
- No hardcoded keys or secrets in telemetry code
