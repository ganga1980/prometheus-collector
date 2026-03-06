# Test Authoring

## Description
Guides creating tests following the Ginkgo E2E framework and Go testing conventions used in this repository.

USE FOR: add test, write test, test coverage, test for feature, add unit test, add integration test, add e2e test, ginkgo test
DO NOT USE FOR: fixing a flaky test, refactoring tests, test infrastructure changes

## Instructions

### When to Apply
When adding tests for new features, increasing coverage, or adding regression tests for bug fixes.

### Step-by-Step Procedure
1. **Determine test type:**
   - **Unit test** → `*_test.go` in the same package as the source.
   - **E2E test** → Ginkgo suite in `otelcollector/test/ginkgo-e2e/<suite>/`.
2. **For Go unit tests:**
   - Create `<filename>_test.go` next to the source file.
   - Use standard `testing` package with table-driven tests.
   - Run: `go test ./...` in the module directory.
3. **For Ginkgo E2E tests:**
   - Use Ginkgo v2 `Describe`/`Context`/`It` structure with Gomega matchers.
   - Add test labels using constants from `otelcollector/test/utils/constants.go`.
   - Add scrape job configs in `otelcollector/test/test-cluster-yamls/` if needed.
   - Use shared utilities from `otelcollector/test/ginkgo-e2e/utils/` (e.g., `SetupKubernetesClient`, `ParseK8sYaml`).
4. **For TypeScript tests:**
   - Create `<filename>.test.ts` alongside the source file.
   - Use Jest with `ts-jest` preset.
   - Run: `cd tools/az-prom-rules-converter && npm test`.
5. **Register new test labels** (if adding a label):
   - Add constant in `otelcollector/test/utils/constants.go`.
   - Document in `otelcollector/test/README.md`.
   - Add to `.github/pull_request_template.md` checklist.
   - Add to `otelcollector/test/testkube/testkube-test-crs.yaml`.

### Files Typically Involved
- `otelcollector/test/ginkgo-e2e/*/` — E2E test suites
- `otelcollector/test/utils/constants.go` — test label constants
- `otelcollector/test/test-cluster-yamls/` — test cluster configs
- `otelcollector/test/testkube/testkube-test-crs.yaml` — Testkube CRs

### Validation
- `go test -v ./...` passes in the module directory
- New test label documented (if applicable)
- Test is included in Testkube CR (if new suite)

## Examples from This Repo
- `f092846` — test: add Ginkgo E2E tests for operator label
- `afc1fc7` — test: migrate tests to Testkube framework
- `b03f03f` — test: add nightly build test validation
