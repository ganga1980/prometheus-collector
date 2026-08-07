# Bug Fix

## Description
Guides structured bug fix workflow with root cause analysis, regression testing, and proper commit formatting.

USE FOR: fix bug, resolve issue, patch, hotfix, debug, error fix
DO NOT USE FOR: feature development, refactoring, performance optimization

## Instructions

### When to Apply
When fixing bugs reported through issues, test failures, or production incidents.

### Step-by-Step Procedure
1. Reproduce the issue: identify the affected component and failure mode
2. Read related source code in the affected `otelcollector/` module
3. Identify root cause and verify with test/log output
4. Implement the fix in the minimal set of files needed
5. Add a regression test (Ginkgo E2E or unit test) that would have caught the bug
6. Build: `cd otelcollector/opentelemetry-collector-builder && make all`
7. Run affected test suites
8. Commit with `fix:` prefix: `fix: description of what was fixed (#issue)`

### Files Typically Involved
- Source files under `otelcollector/` (varies by bug)
- Test files under `otelcollector/test/ginkgo-e2e/` (regression test)
- `RELEASENOTES.md` if user-facing

### Validation
- `make all` succeeds
- Regression test passes
- Existing tests still pass
- Commit message uses `fix:` prefix

## Examples from This Repo
- `fix: upgrade ME to fix otel bug`
- `fix: update acstor node-agent pod selector for label changes (#1369)`
- `fix: proxy basic auth for mdsd (#1383)`
- `BUG: Missing log columns: Add pod and containerID columns to logged output (#1398)`
