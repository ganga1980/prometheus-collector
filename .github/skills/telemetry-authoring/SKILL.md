# Telemetry Authoring

## Description
Guides adding telemetry instrumentation following the existing Application Insights and OpenTelemetry patterns in this repository.

USE FOR: add telemetry, add metrics, add tracing, add observability, instrument code, track event, emit metric, add logging, add Application Insights, add OpenTelemetry, telemetry gap, missing telemetry
DO NOT USE FOR: fixing broken telemetry pipelines, configuring telemetry infrastructure (Fluent Bit, Log Analytics), dashboard creation, alert rule authoring

## Instructions

### Telemetry Pattern Discovery
Before adding ANY telemetry, identify the existing patterns:

1. **Primary telemetry SDK**: Application Insights via `otelcollector/fluent-bit/src/telemetry.go` — custom wrapper using Microsoft ApplicationInsights-Go library.
2. **Telemetry client**: Initialized per Azure cloud environment (Prod, Fairfax, Mooncake, USSec, USNat, Bleu).
3. **Environment variables for keys**: `APPLICATIONINSIGHTS_AUTH`, `APPLICATIONINSIGHTS_AUTH_USGOVERNMENT`, `TELEMETRY_APPLICATIONINSIGHTS_KEY`.
4. **Logging**: Standard `log` package in main, `log/slog` in receiver code, custom lumberjack-based logger in Fluent Bit.

### What to Instrument (Priority Order)

**a. Error paths (highest priority)**
- Every `if err != nil` block for unexpected failures.
- Include: error type, message, operation context.
- Pattern: Use existing telemetry helpers in `fluent-bit/src/telemetry.go`.

**b. Entry points and API boundaries**
- HTTP handlers, gRPC endpoints, ConfigMap reload handlers.
- Track: operation name, duration, success/failure.

**c. External calls**
- Azure Monitor API calls, Kubernetes API calls, target allocator HTTP calls.
- Track: target service, duration, response status.

**d. Critical business logic**
- Config parsing completion, scrape target assignment, metric flush operations.
- Track: custom events with dimensions (`computer`, `controller_type`, `container_type`).

### Telemetry Conventions
- **Metric naming**: `<component>.<operation>.<measurement>` (e.g., `container_inventory.collect.duration`).
- **Event naming**: `<ComponentAction>` (e.g., `KubeInventoryCollected`, `FlushFailed`).
- **Standard dimensions**: `computer`/hostname, `controller_type` (DaemonSet/ReplicaSet), `container_type`, operation context.
- **Error telemetry**: Must include error class, message, and source location.

### Anti-Patterns to Avoid
- Do NOT log sensitive data (credentials, tokens, PII).
- Do NOT add telemetry inside tight loops.
- Do NOT use `fmt.Println` for production telemetry — use the structured SDK.
- Do NOT create new TelemetryClient instances — reuse the existing shared instance.
- Do NOT emit telemetry in unit test code paths (respect test guards).
- Do NOT hardcode instrumentation keys — use environment variables.

### Validation
- Verify telemetry import matches existing files.
- Verify metric/event names follow the naming convention.
- Verify dimensions match the standard set.
- Run `go test ./...` to ensure no test isolation issues.
