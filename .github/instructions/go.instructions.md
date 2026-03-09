---
applyTo: "**/*.go"
description: "Go code style, patterns, and best practices for this repository."
---

# Go Code Conventions

1. **Error handling is mandatory** — Every function returning `error` must be checked. Use `log.Fatal(err)` for startup failures, `shared.EchoError(err.Error())` for recoverable errors. Never use `_` to discard errors from I/O, network, or config operations.
2. **Import grouping** — Group imports: (1) standard library, (2) third-party (Azure SDK, OTel, K8s), (3) internal packages. Separate groups with blank lines.
3. **Naming** — Variables and functions use `camelCase`. Exported types use `PascalCase`. Package names are lowercase single words.
4. **Environment config** — Use `os.Getenv("KEY")` or `shared.GetEnv("KEY", "default")` for configuration. Never hardcode cluster names, regions, or connection strings.
5. **Graceful shutdown** — Long-running services must handle `SIGTERM`/`SIGINT` via `signal.Notify`. See `otelcollector/main/main.go` for the pattern.
6. **Module boundaries** — Each directory with `go.mod` is an independent module. Run `go mod tidy` after dependency changes. Check `go.sum` in.
7. **OTel patterns** — Use the existing OTel Collector framework for receivers, processors, and exporters. Follow `otelcollector/prometheusreceiver/` for custom component patterns.
8. **Testing** — Use Ginkgo `DescribeTable`/`Entry` for parameterized tests. Use `gomega` matchers (`Expect(err).NotTo(HaveOccurred())`). Place E2E tests under `otelcollector/test/ginkgo-e2e/`.
9. **Logging** — Use the logging approach of the component you're modifying: `log` (main), `slog` (prometheus-ui), `ctrl.Log` (allocator). Don't introduce new logging libraries.
10. **No `fmt.Println` in production** — Use structured logging or `shared.Echo*` helpers for output that should be visible in production logs.
