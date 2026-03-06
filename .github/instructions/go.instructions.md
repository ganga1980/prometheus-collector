---
applyTo: "**/*.go"
description: "Go code style, error handling, and conventions for this repository."
---

# Go Conventions

- Use `camelCase` for unexported identifiers, `PascalCase` for exported identifiers, `UPPERCASE` for constants.
- Group imports: standard library first, then external packages, separated by blank lines. Alias Kubernetes packages (e.g., `corev1 "k8s.io/api/core/v1"`).
- Wrap all errors with context: `fmt.Errorf("doing something: %w", err)`. Never discard errors silently.
- Use `log.Fatalf()` for unrecoverable startup errors. Use `log/slog` for structured logging in receiver code.
- Access secrets via environment variables using `os.Getenv()` or the `shared.GetEnv(name, default)` helper — never hardcode secrets.
- When modifying `go.mod`, always run `go mod tidy` afterward. Note that this repo has 24 `go.mod` files — check if your dependency change affects multiple modules.
- Build binaries with security flags: `-buildmode=pie -ldflags '-linkmode external -extldflags=-Wl,-z,now'`.
- Test files use `*_test.go` naming. E2E tests use Ginkgo v2 with Gomega matchers in `otelcollector/test/ginkgo-e2e/`.
- The `otelcollector/shared/` module uses local `replace` directives — do not change these to remote paths.
- Check for `OS_TYPE` and `CONTROLLER_TYPE` environment variables when code behavior differs between Linux/Windows or DaemonSet/ReplicaSet modes.
