# Test Framework Guide

## Test Decision Tree

When adding tests, use this decision tree:

1. **Testing metric query results against Azure Monitor?** → Ginkgo E2E in `querymetrics/`
2. **Testing container health, process readiness?** → Ginkgo E2E in `containerstatus/`
3. **Testing config processing/validation?** → Ginkgo E2E in `configprocessing/`
4. **Testing liveness probe restart behavior?** → Ginkgo E2E in `livenessprobe/`
5. **Testing Prometheus UI API?** → Ginkgo E2E in `prometheusui/`
6. **Testing operator functionality?** → Ginkgo E2E in `operator/`
7. **Testing region-specific behavior?** → Ginkgo E2E in `regionTests/`
8. **Testing pure Go logic without cluster?** → Unit test with `testing` package alongside source file
9. **Testing Prometheus receiver internals?** → Unit test in `otelcollector/prometheusreceiver/`

## Test Patterns in This Repo

### Ginkgo E2E Tests (Primary)
- **Framework**: Ginkgo v2 (`github.com/onsi/ginkgo/v2`) with Gomega matchers
- **Location**: `otelcollector/test/ginkgo-e2e/<suite>/`
- **Naming**: `*_test.go` with `Describe`/`It`/`DescribeTable` blocks
- **Shared utilities**: `otelcollector/test/ginkgo-e2e/utils/`
  - `constants.go` — error exclusion lists, test labels
  - `kubernetes_api_utils.go` — pod listing, container inspection
  - `amw_query_api_utils.go` — Azure Monitor workspace query helpers
  - `prometheus_ui_api_utils.go` — Prometheus UI API helpers
  - `setup_utils.go` — test environment setup
  - `operator_utils.go` — operator-specific helpers

### Ginkgo Test Structure
```go
package mytest

import (
    . "github.com/onsi/ginkgo/v2"
    . "github.com/onsi/gomega"
    "prometheus-collector/otelcollector/test/utils"
)

var _ = Describe("My Feature", func() {
    It("should behave correctly", func() {
        // Use utils for queries and assertions
        warnings, result, err := utils.InstantQuery(client, query)
        Expect(err).NotTo(HaveOccurred())
        Expect(result).NotTo(BeEmpty())
    })

    DescribeTable("should work for multiple inputs",
        func(input string, expected string) {
            Expect(process(input)).To(Equal(expected))
        },
        Entry("case 1", "input1", "output1"),
        Entry("case 2", "input2", "output2"),
    )
})
```

### Go Unit Tests
- **Framework**: Standard `testing` package
- **Location**: Alongside source files (e.g., `shared/health_metrics_test.go`)
- **Naming**: `*_test.go` with `Test*` functions

### Test Labels
Tests can be filtered by labels: `operator`, `windows`, `arm64`, `arc-extension`, `fips`, `linux-daemonset-custom-config`

Add new labels to:
1. `otelcollector/test/utils/constants.go`
2. `otelcollector/test/README.md`
3. `.github/pull_request_template.md`
4. `otelcollector/test/testkube/testkube-test-crs.yaml`

## Test Data
- **Scrape configs**: `otelcollector/test/test-cluster-yamls/` — ConfigMaps and custom resource YAML files
- **Error exclusions**: `otelcollector/test/ginkgo-e2e/utils/constants.go` — `LogLineErrorsToExclude` list
- **Test cluster setup**: See `otelcollector/test/README.md` for bootstrapping instructions

## Common Test Utilities
- `utils.InstantQuery()` — Execute Prometheus instant queries
- `utils.GetPodList()` — List pods via Kubernetes API
- Error log checking with configurable exclusion list
- Container process verification for expected running processes
