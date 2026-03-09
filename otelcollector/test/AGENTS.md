# Test Framework Guide

## Test Decision Tree

When adding tests, use this decision tree:

1. **Pure Go logic with no Kubernetes dependency?** → Unit test (`go test`)
   - Place in same directory as source: `*_test.go`
   - Use standard `testing` package or Ginkgo

2. **Needs a running Kubernetes cluster?** → E2E test (Ginkgo)
   - Place in `otelcollector/test/ginkgo-e2e/<suite>/`
   - Use `DescribeTable` + `Entry` with labels

3. **Tests config parsing or validation?** → `configprocessing` suite
   - `otelcollector/test/ginkgo-e2e/configprocessing/`

4. **Tests metric collection or query results?** → `querymetrics` suite
   - `otelcollector/test/ginkgo-e2e/querymetrics/`

5. **Tests Prometheus Operator CRDs?** → `operator` suite
   - `otelcollector/test/ginkgo-e2e/operator/`

6. **Tests container lifecycle?** → `containerstatus` suite
   - `otelcollector/test/ginkgo-e2e/containerstatus/`

7. **Tests TypeScript rules converter?** → Jest
   - `cd tools/az-prom-rules-converter && npm test`

## Test Patterns in This Repo

### E2E Tests (Ginkgo v2 + Gomega)
- **Framework:** Ginkgo v2 (`github.com/onsi/ginkgo/v2`) + Gomega (`github.com/onsi/gomega`)
- **Pattern:**
  ```go
  var _ = DescribeTable("Description",
    func(namespace string, controllerLabelName string, controllerLabelValue string, containerName string, labels []string) {
      err := utils.CheckIfAllContainersAreRunning(K8sClient, namespace, controllerLabelName, controllerLabelValue, containerName)
      Expect(err).NotTo(HaveOccurred())
    },
    Entry("when checking ama-metrics replica", "kube-system", "rsName", "ama-metrics", "ama-metrics",
      Label(utils.ConfigProcessingCommon)),
  )
  ```
- **Labels:** `ConfigProcessingCommon`, `operator`, `windows`, `arm64`, `arc-extension`, `fips`
- **Location:** `otelcollector/test/ginkgo-e2e/`
- **Naming:** `*_test.go` suffix

### Unit Tests
- Standard Go `testing` package
- Location: alongside source files (e.g., `prometheusreceiver/generated_component_test.go`)

### TypeScript Tests (Jest)
- Location: `tools/az-prom-rules-converter/`
- Naming: `*.test.ts`
- Run: `npm test`

## Common Test Utilities
- `otelcollector/test/ginkgo-e2e/utils/constants.go` — Test label constants
- `otelcollector/test/ginkgo-e2e/utils/` — Shared helper functions (container checks, metric queries, K8s client setup)

## Test Data
- Test cluster YAML manifests: `otelcollector/test/test-cluster-yamls/`
- TestKube integration: `otelcollector/test/testkube/`
- Arc conformance: `otelcollector/test/arc-conformance/`
