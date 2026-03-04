# Bug Fix

## Description
Structured workflow for fixing bugs in the prometheus-collector agent, including proper diagnosis, fix implementation, and regression testing.

USE FOR: fix bug, resolve issue, patch, hotfix, debug, error fix, crash fix
DO NOT USE FOR: feature development, refactoring, performance optimization, dependency updates

## Instructions

### When to Apply
When resolving a reported issue, fixing a crash, correcting a misconfiguration, or addressing unexpected behavior in the agent.

### Step-by-Step Procedure
1. **Reproduce**: Identify the failing behavior. Check container logs, Application Insights telemetry, or test output.
2. **Locate**: Find the affected component:
   - OTel Collector issues → `otelcollector/opentelemetry-collector-builder/`
   - Config validation → `otelcollector/prom-config-validator-builder/`
   - Telemetry/health → `otelcollector/fluent-bit/src/`
   - Startup/lifecycle → `otelcollector/main/`
   - Shared utilities → `otelcollector/shared/`
   - Target allocation → `otelcollector/otel-allocator/`
   - Helm chart → `otelcollector/deploy/`
3. **Fix**: Apply the minimal change to resolve the issue.
4. **Test**: Add a regression test or explain why one isn't feasible.
5. **Verify multi-platform**: If the fix touches OS-specific code, verify both Linux and Windows paths.
6. **Build**: Run `make` in the affected component directory.
7. **Commit**: Use `fix:` prefix in commit message (e.g., `fix: correct node affinity syntax in ama-metrics DS`).

### Files Typically Involved
- `otelcollector/shared/*.go` — common utility fixes
- `otelcollector/fluent-bit/src/*.go` — telemetry plugin fixes
- `otelcollector/main/main.go` — startup/lifecycle fixes
- `otelcollector/deploy/addon-chart/` — Helm chart fixes
- `otelcollector/scripts/*.sh` — setup script fixes

### Validation
- Build succeeds for affected component (`make` in component directory)
- Existing E2E tests pass
- Regression test added (or justification provided)
- No new Trivy vulnerabilities introduced

## Examples from This Repo
- `fix: upgrade ME to fix otel bug` (716a29e)
- `fix: update acstor node-agent pod selector for label changes (#1369)` (81b03f2)
- `BUG: Missing log columns: Add pod and containerID columns to logged output (#1398)` (3b36c58)

## References
- `.github/pull_request_template.md` — PR checklist for fixes
- `otelcollector/test/README.md` — how to run E2E tests
