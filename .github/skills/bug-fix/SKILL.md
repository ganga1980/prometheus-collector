# Bug Fix

## Description
Structured bug fix workflow for the prometheus-collector, ensuring proper regression testing and commit formatting.

USE FOR: fix bug, resolve issue, patch, hotfix, debug, error fix
DO NOT USE FOR: feature development, refactoring, performance optimization

## Instructions

### When to Apply
When fixing a defect in collector behavior, configuration parsing, metric collection, or deployment. Bug fixes represent ~16% of commits (42/year).

### Step-by-Step Procedure
1. **Reproduce the issue.** Identify the affected component (collector, config reader, allocator, fluent-bit, Helm chart).
2. **Locate the root cause.** Trace through the relevant code path in `otelcollector/`.
3. **Write a regression test** before fixing. Place test in the same package as `*_test.go`, or add a Ginkgo E2E test in `otelcollector/test/ginkgo-e2e/`.
4. **Apply the fix.** Follow Go conventions: wrap errors with `fmt.Errorf("...: %w", err)`, use structured logging.
5. **Build:** `cd otelcollector && go build ./...`
6. **Test:** `cd otelcollector && go test ./...`
7. **Commit** with format: `fix: <concise description of what was fixed>`

### Files Typically Involved
- `otelcollector/main/main.go` — startup/shutdown fixes
- `otelcollector/prometheusreceiver/` — scraping behavior fixes
- `otelcollector/configmapparser/` — config parsing fixes
- `otelcollector/shared/` — shared utility fixes
- `otelcollector/fluent-bit/src/` — telemetry plugin fixes

### Validation
- Regression test exists and passes
- `go build ./...` succeeds
- `go test ./...` passes
- No unrelated behavior changes

## Examples from This Repo
- `3b36c58` — fix: configuration parsing for custom scrape configs
- `81b03f2` — fix: auth token handling in target allocator
- `e8867d0` — fix: encoding issue in fluent-bit plugin
