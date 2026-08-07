# Test Authoring

## Description
Create Ginkgo/Gomega tests following the repository's BDD testing patterns.

USE FOR: add test, write test, test coverage, test for feature, add unit test, add integration test, TDD, test-driven development
DO NOT USE FOR: fixing a flaky test, refactoring tests, test infrastructure changes (use ci-cd-pipeline skill)

## Instructions

### When to Apply
When adding tests for new features, bug regression tests, or improving coverage (38 commits/year, 14.0%).

### Step-by-Step Procedure
1. **Choose test type:**
   - **Unit test:** Pure logic, no K8s cluster needed → place in the module directory as `*_test.go`
   - **E2E test:** Requires running K8s cluster → place in `otelcollector/test/ginkgo-e2e/<suite>/`
2. **Follow existing patterns:**
   ```go
   var _ = DescribeTable("Description of test group",
     func(namespace string, controllerLabelName string, ...) {
       err := utils.SomeTestHelper(K8sClient, namespace, ...)
       Expect(err).NotTo(HaveOccurred())
     },
     Entry("description", "kube-system", "rsName", "ama-metrics",
       Label(utils.ConfigProcessingCommon)),
   )
   ```
3. **Add test labels** — Define new labels in `otelcollector/test/ginkgo-e2e/utils/constants.go`.
4. **Use shared utilities** — Check `otelcollector/test/ginkgo-e2e/utils/` for existing helpers (container checks, metric queries, etc.).
5. **Update documentation:**
   - Add label to `otelcollector/test/README.md`
   - Add label to `.github/pull_request_template.md`
   - Add label to `otelcollector/test/testkube/testkube-test-crs.yaml` if needed

### Files Typically Involved
- `otelcollector/test/ginkgo-e2e/<suite>/*_test.go`
- `otelcollector/test/ginkgo-e2e/utils/constants.go`
- `otelcollector/test/ginkgo-e2e/utils/*.go` (shared helpers)
- `otelcollector/test/README.md`

### Validation
- Tests compile: `go test -c ./...` in the suite directory
- Tests pass locally (unit) or in CI (E2E)
- Test labels are documented
- TDD workflow: write failing test → implement code → verify test passes

## Examples from This Repo
- `test: minimal ingestion profile test cases for no configmap, configmap v1 and v2 (#1305)`
- `test: Testkube workflow migration (#1392)`
- `test: Added FIC auth support to arc conformance tests (#1338)`

## References
- `otelcollector/test/README.md` — Test framework documentation
- `otelcollector/test/ginkgo-e2e/utils/` — Shared test utilities
- [Ginkgo v2 docs](https://onsi.github.io/ginkgo/)
