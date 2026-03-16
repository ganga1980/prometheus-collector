# Test Authoring

## Description
Guides creating Ginkgo E2E tests and Jest unit tests following this repo's test patterns, naming conventions, and infrastructure.

USE FOR: add test, write test, test coverage, add E2E test, add unit test, add Ginkgo test, TDD
DO NOT USE FOR: fixing flaky tests, refactoring test infrastructure, TestKube workflow changes

## Instructions

### When to Apply
When adding tests for new features, bug fixes requiring regression tests, or improving test coverage.

### Step-by-Step Procedure

#### Ginkgo E2E Tests (Go)
1. Identify the appropriate test suite under `otelcollector/test/ginkgo-e2e/` or create a new one
2. For a new suite: create a directory, add `suite_test.go` with Ginkgo bootstrap, add `go.mod`
3. Write tests using Ginkgo `Describe`/`It`/`Expect` patterns — follow existing test files
4. Use shared utilities from `otelcollector/test/ginkgo-e2e/utils/` (constants, helpers)
5. Add test label constants to `otelcollector/test/utils/constants.go`
6. If new scrape jobs are needed, add configs to `otelcollector/test/test-cluster-yamls/`
7. Update `otelcollector/test/testkube/testkube-test-crs.yaml` for new test suites
8. Run: `cd otelcollector/test/ginkgo-e2e/<suite> && go test -v ./...`

#### Jest Unit Tests (TypeScript)
1. Create test file alongside source: `src/<name>.test.ts`
2. Use Jest `describe`/`it`/`expect` patterns
3. Run: `cd tools/az-prom-rules-converter && npm test`

### Files Typically Involved
- `otelcollector/test/ginkgo-e2e/<suite>/*_test.go`
- `otelcollector/test/ginkgo-e2e/<suite>/suite_test.go`
- `otelcollector/test/ginkgo-e2e/utils/constants.go`
- `otelcollector/test/test-cluster-yamls/`
- `otelcollector/test/testkube/testkube-test-crs.yaml`
- `tools/az-prom-rules-converter/src/*.test.ts`

### Validation
- All tests pass locally
- Test labels documented in `otelcollector/test/README.md`
- PR template Tests Checklist completed

## Examples from This Repo
- `test: minimal ingestion profile test cases for no configmap, configmap v1 and v2 (#1305)`
- `test: Testkube workflow migration (#1392)`
- `test: Increase nightly tests timeout (#1399)`
