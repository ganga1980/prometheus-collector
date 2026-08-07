# Repository Instructions

## Summary

Azure Monitor Prometheus Collector — a custom OpenTelemetry Collector distribution (v0.144.0) for collecting Prometheus metrics in Azure Kubernetes Service (AKS) and Arc-enabled clusters. Primary language is **Go** (~45%), with TypeScript tooling, Shell scripts, and Bicep/Terraform/ARM deployment templates. Runs as a DaemonSet and ReplicaSet in Kubernetes, using Fluent Bit for telemetry forwarding to Application Insights.

## General Guidelines

1. Follow existing Go conventions: `snake_case` for file names, `camelCase` for local variables, `PascalCase` for exported identifiers.
2. All new code must have accompanying Ginkgo E2E tests or unit tests. See PR template checklist.
3. Use the `#fill-pr-template` skill when preparing PRs — the repo has a detailed PR template at `.github/pull_request_template.md`.
4. If newer commits make prior changes unnecessary, revert them rather than layering fixes.
5. Load `.github/instructions/go.instructions.md` for Go code conventions.
6. Never hardcode secrets — use environment variables (`APPLICATIONINSIGHTS_AUTH_PUBLIC`, etc.).
7. Break complex tasks into smaller prompts — one module, one function, one test at a time.
8. Be specific: reference actual file paths and function names from this repo.
9. Always validate AI-generated code: run `make all` in the builder directory, then run Ginkgo tests.

## Prompting Best Practices

1. Break complex tasks into smaller, focused prompts (one module, one function, one test at a time).
2. Be specific: reference actual file paths, function names, and patterns from this repo (e.g., `otelcollector/opentelemetry-collector-builder/main.go`).
3. Provide examples of expected inputs/outputs when asking for implementations.
4. Open relevant files before prompting — Copilot uses open files as context.
5. Start new chat sessions for unrelated tasks to avoid context pollution.
6. Use the explore → plan → code → commit workflow for complex changes (see `AGENTS.md`).
7. Always validate AI-generated code: review for correctness, run `make all`, and check Ginkgo tests.

## Build Instructions

- **Bootstrap:** `cd otelcollector/opentelemetry-collector-builder && go mod download`
- **Build all components:** `cd otelcollector/opentelemetry-collector-builder && make all`
- **Build individual:** `make otelcollector`, `make fluentbitplugin`, `make promconfigvalidator`, `make targetallocator`, `make configurationreader`, `make prometheusui`
- **TypeScript tool:** `cd tools/az-prom-rules-converter && npm install && npm run build`
- **Run TypeScript tests:** `cd tools/az-prom-rules-converter && npm test`
- **Ginkgo E2E tests:** See `otelcollector/test/README.md` for cluster bootstrap instructions. Run via TestKube or `go test -v ./...` in each `ginkgo-e2e/<suite>/` directory.
- **Mixins:** `cd mixins/kubernetes && make` (also `mixins/node`, `mixins/coredns`)

## Task-Specific Skills

| Skill | Triggers | Description |
|-------|----------|-------------|
| `#dependency-update` | update dependency, bump package, upgrade library | Safe dependency update workflow for Go modules |
| `#feature-development` | add feature, implement, new component | New feature scaffolding with tests and config |
| `#test-authoring` | add test, write test, add E2E test | Create Ginkgo E2E or unit tests following repo patterns |
| `#bug-fix` | fix bug, resolve issue, hotfix | Bug fix workflow with regression test requirements |
| `#documentation` | update docs, update README, release notes | Documentation update workflow |
| `#infrastructure` | update chart, helm, bicep, Dockerfile | Infrastructure and deployment template changes |
| `#ci-cd-pipeline` | update workflow, CI/CD, pipeline | CI/CD workflow modification |
| `#security-review` | security review, STRIDE analysis | STRIDE-based security review |
| `#telemetry-authoring` | add telemetry, add metrics, instrument code | Add telemetry following Application Insights patterns |
| `#fix-critical-vulnerabilities` | fix CVE, trivy fix, vulnerability fix | Fix critical/high vulnerabilities using Trivy |

## Known Patterns & Gotchas

- The repo has **24 Go modules** — dependency updates may need to touch multiple `go.mod` files.
- Dependabot is configured to ignore `go.opentelemetry.io/collector*` and `opentelemetry-collector-contrib*` updates in some modules (managed via manual OTel upgrade workflow).
- OTel Collector version upgrades use the automated `otelcollector-upgrade` GitHub Action and `internal/otel-upgrade-scripts/upgrade.sh`.
- Multi-arch builds (amd64/arm64) — Dockerfiles use `BUILDPLATFORM`/`TARGETARCH` for cross-compilation.
- The `.trivyignore` file contains accepted CVEs with justification — check before adding new entries.
- Fluent Bit plugin is a Go CGO shared library (`.so`) — requires `CGO_ENABLED=1`.
