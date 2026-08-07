# Repository Instructions

## Summary

This is the **Azure Monitor managed service for Prometheus** collector (`prometheus-collector`). It collects Prometheus metrics from Kubernetes clusters (AKS, Arc-enabled) and sends them to Azure Monitor. Primary languages: Go (35%), YAML/Helm (51%), with TypeScript tooling and Shell/PowerShell scripts. Built on OpenTelemetry Collector (v0.144.0) with custom Prometheus receiver, deployed as Helm charts on Kubernetes.

## General Guidelines

1. Follow existing code patterns — sample 3-5 nearby files before writing new code.
2. All Go code must handle errors explicitly; never ignore returned `error` values.
3. Kubernetes manifests go under `otelcollector/deploy/`; IaC templates under `Addon*Template/` or `Arc*Template/`.
4. Break complex tasks into smaller prompts — one module, one function, one test at a time.
5. Be specific: reference actual file paths and function names from this repo.
6. Always validate AI-generated code: run `make` in the relevant module, then `go test ./...`.
7. Use the explore → plan → code → commit workflow for multi-file changes (see `AGENTS.md`).
8. Commit messages should follow Conventional Commits format (`feat:`, `fix:`, `test:`, `build(deps):`).

## Prompting Best Practices

1. Open relevant files before prompting — Copilot uses open files as context.
2. Reference specific paths: `otelcollector/main/main.go`, `otelcollector/deploy/addon-chart/`, etc.
3. Provide examples of expected metric names, label formats, or config structures.
4. Start new chat sessions for unrelated tasks to avoid context pollution.
5. For Helm chart changes, always check both `addon-chart/` and `chart/prometheus-collector/` templates.

## Custom Agents

| Agent | Triggers | Description |
|-------|----------|-------------|
| @CodeReviewer | review PR, review code | Review PRs for correctness, style, security, telemetry gaps |
| @SecurityReviewer | security review, threat model | Deep STRIDE security analysis and attack surface review |
| @ThreatModelAnalyst | threat model analysis | Generate persistent STRIDE threat model artifacts under `threat-model/` |
| @DocumentWriter | write docs, update README | Create and maintain documentation following repo conventions |
| @prd | create PRD, write requirements | Generate structured Product Requirements Documents |

## Task-Specific Skills

| Skill | Triggers | Description |
|-------|----------|-------------|
| dependency-update | update dependency, bump package | Safe dependency updates with testing |
| bug-fix | fix bug, resolve issue, hotfix | Structured bug fix with regression test |
| feature-development | add feature, implement, new endpoint | New feature scaffolding |
| test-authoring | add test, write test, TDD | Create Ginkgo/Gomega tests following repo patterns |
| ci-cd-pipeline | update pipeline, fix CI | Modify GitHub Actions or Azure Pipelines |
| infrastructure | update Helm chart, modify Dockerfile | Infrastructure and deployment changes |
| security-review | security review, STRIDE analysis | STRIDE-based security review |
| telemetry-authoring | add telemetry, add metrics | Add OpenTelemetry instrumentation |
| fix-critical-vulnerabilities | fix CVE, trivy fix | Fix critical/high vulnerabilities |

## Build Instructions

- **Go modules:** `cd otelcollector/opentelemetry-collector-builder && make` (or specific module)
- **TypeScript tool:** `cd tools/az-prom-rules-converter && npm install && npm run build`
- **Tests:** `cd otelcollector/test/ginkgo-e2e/<suite> && go test -v ./...`
- **Helm lint:** `helm lint otelcollector/deploy/addon-chart/azure-monitor-metrics-addon/`
- **Mixins:** `cd mixins/kubernetes && make all`

## Known Patterns & Gotchas

- Multiple `go.mod` files exist — ensure you're in the correct module directory.
- OpenTelemetry versions are pinned via `OPENTELEMETRY_VERSION` file — don't bump independently.
- Dependabot ignores OTel core packages; those are upgraded manually via `otelcollector-upgrade.yml`.
- Windows and Linux Dockerfiles must stay in sync for feature parity.
- Helm chart changes may need updates in both `addon-chart/` and `chart/prometheus-collector/`.
- E2E tests require a live Kubernetes cluster; see `otelcollector/test/README.md`.
- `.trivyignore` contains intentional CVE exemptions with justifications.
