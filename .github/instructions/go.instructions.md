---
applyTo: "**/*.go"
description: "Go coding conventions for the prometheus-collector repository"
---

# Go Conventions

1. **Error handling**: Always check `err != nil`. Use `log.Fatalf` for unrecoverable errors, `log.Printf` with return for recoverable ones. Never silently ignore errors.
2. **Naming**: Exported functions use `PascalCase`, unexported use `camelCase`. Acronyms stay uppercase (`HTTP`, `URL`, `API`).
3. **Environment variables**: Use `os.Getenv()` or `shared.GetEnv(key, defaultVal)` — never hardcode configuration values or secrets.
4. **Logging**: Prefix log messages with the component name (e.g., `"prom-config-validator::"`, `"out_appinsights::"`). Use `log.Println`/`log.Printf` from the standard library.
5. **OS-specific code**: Use file suffixes `_linux.go` and `_windows.go` for platform-specific implementations. Use `//go:build` directives.
6. **Local module references**: Use `replace` directives in `go.mod` for intra-repo dependencies (e.g., `replace github.com/prometheus-collector/shared => ./shared`).
7. **CGO**: The Fluent Bit plugin requires `CGO_ENABLED=1`. Other components use `CGO_ENABLED=1` with `-buildmode=pie` for security hardening.
8. **Imports**: Group imports as: stdlib, third-party, local packages. Use blank imports for side effects only.
9. **Telemetry**: Use the `appinsights.TelemetryClient` singleton via the Fluent Bit plugin or `shared.SetupTelemetry()`. Do not create new telemetry clients. Gate telemetry with `TELEMETRY_DISABLED` env var.
10. **Concurrency**: Use goroutines for background telemetry collection (e.g., `go ExposePrometheusCollectorHealthMetrics()`). Use `sync.Mutex` or `sync.RWMutex` for shared state.
11. **Tests**: Use Ginkgo v2 (`github.com/onsi/ginkgo/v2`) with Gomega matchers for E2E tests. Use standard `testing` package for unit tests.
12. **Build flags**: Use `-buildmode=pie -ldflags '-linkmode external -extldflags=-Wl,-z,now'` for security-hardened builds.
