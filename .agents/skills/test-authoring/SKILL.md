# Test Authoring

## Description
Guides writing new Ginkgo E2E tests or Go unit tests following the patterns established in this repository.

USE FOR: add test, write test, test coverage, test for feature, add unit test, add integration test, add e2e test, ginkgo test
DO NOT USE FOR: fixing a flaky test, refactoring tests, test infrastructure changes, updating test cluster YAML

## Instructions

### When to Apply
When adding test coverage for new features, bug fixes, or uncovered code paths.

### Step-by-Step Procedure

#### For Ginkgo E2E Tests
1. Determine the appropriate test suite in `otelcollector/test/ginkgo-e2e/`:
   - `configprocessing` — Config validation tests
   - `containerstatus` — Container health checks
   - `livenessprobe` — Process restart behavior
   - `operator` — Operator functionality
   - `prometheusui` — Prometheus UI API
   - `querymetrics` — Metric query validation
   - `regionTests` — Region-specific behavior
2. Add test cases using Ginkgo v2 syntax:
   ```go
   var _ = Describe("Feature Name", func() {
       It("should do expected behavior", func() {
           // Test logic using Gomega matchers
           Expect(result).To(Equal(expected))
       })
   })
   ```
3. Use shared utilities from `otelcollector/test/ginkgo-e2e/utils/`:
   - `utils.InstantQuery()` for Prometheus queries
   - `utils.GetPodList()` for Kubernetes API interactions
4. If adding a new test label, update `otelcollector/test/utils/constants.go`.
5. If a new scrape job is needed, add it to `otelcollector/test/test-cluster-yamls/`.

#### For Go Unit Tests
1. Create `*_test.go` alongside the source file.
2. Use standard `testing` package with table-driven tests.
3. Mock external dependencies; do not call external services.

### Files Typically Involved
- `otelcollector/test/ginkgo-e2e/<suite>/*_test.go`
- `otelcollector/test/ginkgo-e2e/utils/*.go`
- `otelcollector/test/utils/constants.go`
- `otelcollector/test/test-cluster-yamls/`

### Validation
- `go test -v ./...` in the test suite directory
- Test passes on a bootstrapped AKS cluster
- No errors in container logs during test execution

## Examples from This Repo
- `test: minimal ingestion profile test cases for no configmap, configmap v1 and v2 (#1305)`
- `test: Added FIC auth support to arc conformance tests (#1338)`
- `test: Testkube workflow migration (#1392)`

## References
- `otelcollector/test/README.md` — full test documentation
- `.github/pull_request_template.md` — test checklist requirements
