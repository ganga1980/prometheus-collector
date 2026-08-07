# Telemetry Authoring

## Description
Add OpenTelemetry and Prometheus telemetry instrumentation following existing patterns in the collector.

USE FOR: add telemetry, add metrics, add tracing, add observability, instrument code, track event, emit metric, add logging, add OpenTelemetry
DO NOT USE FOR: fixing broken telemetry pipelines, configuring Fluent Bit, dashboard creation, alert rule authoring

## Instructions

### When to Apply
When adding telemetry to new or existing code paths. This repo uses multiple telemetry patterns depending on the component.

### Step-by-Step Procedure

#### 1. Telemetry Pattern Discovery
Before adding telemetry, identify which pattern the target component uses:

| Component | Telemetry SDK | Pattern |
|-----------|--------------|---------|
| Main collector (`otelcollector/main/`) | Standard `log` package + `shared.Echo*` | Simple logging helpers |
| OTel Allocator | OTel SDK + Prometheus exporter | `otelprom.New()` + `sdkmetric.NewMeterProvider()` |
| Prometheus UI | OTel HTTP instrumentation | `otelhttp.NewHandler()` |
| Fluent-bit plugin | Application Insights Go SDK | `appinsights.NewTelemetryClient()` |
| Reference apps | OTel SDK + OTLP exporters | Full OTel metric pipeline |

**NEVER introduce a new telemetry SDK** — always follow the existing pattern in the component.

#### 2. What to Instrument (Priority Order)

a. **Error paths** — Every `if err != nil` that represents unexpected failure:
   ```go
   if err != nil {
       shared.EchoError(fmt.Sprintf("Failed to process config: %v", err))
       // Add error metric or telemetry here
   }
   ```

b. **Entry points** — HTTP handlers, gRPC endpoints, startup sequences:
   - Track: operation name, duration, success/failure
   - Pattern: use `otelhttp` middleware or manual span/timer

c. **External calls** — Outbound HTTP, Kubernetes API calls:
   - Track: target, duration, response status

d. **Critical business logic** — Config reloads, target allocation changes, metric scrape cycles

#### 3. Telemetry Conventions
- **Metric naming:** `<component>.<operation>.<measurement>` (e.g., `collector.scrape.duration`)
- **Standard labels/dimensions:**
  - `computer` / hostname
  - `controller_type` (DaemonSet/ReplicaSet)
  - `container_type`
- **Environment variables for config:**
  - `APPLICATIONINSIGHTS_AUTH_*` — App Insights keys per cloud
  - `OTEL_ENDPOINT` — OTLP exporter endpoint

#### 4. Anti-Patterns to Avoid
- Do NOT log sensitive data (credentials, tokens, PII)
- Do NOT add telemetry in tight loops (excessive volume)
- Do NOT use `fmt.Println` for production telemetry
- Do NOT create new telemetry client instances — reuse shared/singleton
- Do NOT hardcode instrumentation keys — use environment variables

### Validation
- Import statements match existing files in the same module
- Metric/event names follow repo naming convention
- Standard dimensions included
- Build succeeds, existing tests pass

## References
- `otelcollector/main/main.go` — Collector telemetry patterns
- `otelcollector/otel-allocator/main.go` — Allocator OTel setup
- `otelcollector/fluent-bit/src/` — App Insights integration
- `internal/referenceapp/golang/main.go` — Full OTel example
