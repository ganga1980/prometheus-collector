---
applyTo: "**/*.go"
description: "Go code style, conventions, and best practices for this OpenTelemetry Collector repository."
---

# Go Code Conventions

1. **Error handling:** Always check `if err != nil`. Return errors with context using `fmt.Errorf("operation: %w", err)`. Never silently ignore errors.
2. **Logging:** Use `log.Printf`/`log.Println` for standard modules. Fluent Bit plugin uses `FLBLogger.Printf` (custom logger with file rotation via lumberjack). Never use `fmt.Println` in production code.
3. **Imports:** Group in order: stdlib → external packages → internal packages, separated by blank lines.
4. **Naming:** Files use `snake_case.go`. Exported types/functions use `PascalCase`. Local variables use `camelCase`. Constants use `PascalCase` for exported, `camelCase` for unexported.
5. **Environment variables:** Access via `os.Getenv()`. Never hardcode secrets — use env var names like `APPLICATIONINSIGHTS_AUTH_PUBLIC`. Base64-decode keys with `encoding/base64`.
6. **Telemetry:** Use `ApplicationInsights-Go` SDK via shared helpers in `otelcollector/shared/telemetry.go`. Follow existing patterns for `SetupTelemetry()` initialization.
7. **Build flags:** Use `-buildmode=pie -ldflags '-linkmode external -extldflags=-Wl,-z,now'` for security hardening. CGO_ENABLED=1 required for Fluent Bit plugin.
8. **Module structure:** This repo has 24 Go modules. When adding dependencies, update the correct `go.mod` and run `go mod tidy`.
9. **Testing:** Use Ginkgo v2 + Gomega for E2E tests. Place test suites under `otelcollector/test/ginkgo-e2e/<suite>/`. Include `suite_test.go` for Ginkgo bootstrap.
10. **Kubernetes client:** Use `k8s.io/client-go` for cluster interactions. Follow existing patterns in test utilities (`otelcollector/test/ginkgo-e2e/utils/`).
