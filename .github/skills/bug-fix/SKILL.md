# Bug Fix

## Description
Structured workflow for fixing bugs in the prometheus-collector, including regression test requirements.

USE FOR: fix bug, resolve issue, patch, hotfix, debug, error fix
DO NOT USE FOR: feature development, refactoring, performance optimization

## Instructions

### When to Apply
When fixing defects in collector behavior, Helm chart rendering, config parsing, or deployment. This is the second most common pattern (74 commits/year, 27.3%).

### Step-by-Step Procedure
1. **Reproduce** — Identify the failing behavior. Check logs, metrics, or test output.
2. **Locate** — Find the affected code. Common bug areas:
   - `otelcollector/main/main.go` — startup/shutdown issues
   - `otelcollector/configmapparser/` — config parsing errors
   - `otelcollector/deploy/addon-chart/` — Helm template rendering bugs
   - `otelcollector/prometheusreceiver/` — metric collection issues
3. **Fix** — Apply the minimal change that resolves the issue.
4. **Test** — Add a regression test in the appropriate Ginkgo suite or create a unit test.
5. **Build** — Run `make` in the affected module.
6. **Verify** — Run `go test ./...` and confirm the fix.

### Files Typically Involved
- `otelcollector/main/main.go`, `otelcollector/shared/*.go`
- `otelcollector/configmapparser/*.go`
- `otelcollector/deploy/addon-chart/` (Helm templates)
- `otelcollector/test/ginkgo-e2e/` (regression tests)

### Validation
- Build succeeds (`make` or `go build ./...`)
- Existing tests pass (`go test ./...`)
- New regression test added
- Commit message: `fix: <description> (#<issue>)`

## Examples from This Repo
- `fix: upgrade ME to fix otel bug`
- `BUG: Missing log columns: Add pod and containerID columns to logged output (#1398)`
- `fix: update acstor node-agent pod selector for label changes (#1369)`
- `fix: proxy basic auth for mdsd (#1383)`

## References
- `.github/pull_request_template.md` — PR checklist for bug fixes
- `otelcollector/test/README.md` — E2E test setup instructions
