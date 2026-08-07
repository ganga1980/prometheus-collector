# Test Framework Guide

## Test Decision Tree

When adding tests, use this decision tree:

1. **Testing Prometheus metric collection or scrape config behavior?** → Ginkgo E2E test in `configprocessing/`
2. **Testing container status or pod health?** → Ginkgo E2E test in `containerstatus/`
3. **Testing liveness probe behavior?** → Ginkgo E2E test in `livenessprobe/`
4. **Testing Prometheus UI functionality?** → Ginkgo E2E test in `prometheusui/`
5. **Testing metric query results?** → Ginkgo E2E test in `querymetrics/`
6. **Testing operator/CRD behavior?** → Ginkgo E2E test in `operator/`
7. **Testing region-specific behavior?** → Ginkgo E2E test in `regionTests/`
8. **Testing TypeScript rules converter?** → Jest unit test in `tools/az-prom-rules-converter/src/`

## Test Patterns in This Repo

### Ginkgo E2E Tests (Primary)
- **Framework:** Ginkgo v2 + Gomega
- **Location:** `otelcollector/test/ginkgo-e2e/<suite>/`
- **Naming:** `*_test.go` with `suite_test.go` for Ginkgo bootstrap
- **Suites:** `configprocessing`, `containerstatus`, `livenessprobe`, `operator`, `prometheusui`, `querymetrics`, `regionTests`
- **Run command:** `cd otelcollector/test/ginkgo-e2e/<suite> && go test -v ./...`
- **Prerequisites:** Bootstrapped AKS cluster (see `otelcollector/test/README.md`)

### Jest Unit Tests
- **Framework:** Jest + ts-jest
- **Location:** `tools/az-prom-rules-converter/src/`
- **Naming:** `*.test.ts` alongside source files
- **Run command:** `cd tools/az-prom-rules-converter && npm test`

## Common Test Utilities
- `otelcollector/test/ginkgo-e2e/utils/constants.go` — Test label constants, excluded error strings
- `otelcollector/test/ginkgo-e2e/update-go-packages.sh` — Update Go packages across all test modules
- `otelcollector/test/testkube/` — TestKube workflow definitions for CI test execution
- `otelcollector/test/test-cluster-yamls/` — ConfigMaps and custom resources for test clusters

## Test Data
- **ConfigMaps:** `otelcollector/test/test-cluster-yamls/configmaps/`
- **Custom Resources:** `otelcollector/test/test-cluster-yamls/customresources/`
- **Config processing test CRs:** `otelcollector/test/testkube/config-processing-test-crs/`
- **TestKube test definitions:** `otelcollector/test/testkube/testkube-test-crs.yaml`

## Adding New Tests
1. Create a new directory under `otelcollector/test/ginkgo-e2e/` with `go.mod` and `suite_test.go`
2. Add test label constant to `otelcollector/test/utils/constants.go`
3. Add label description to `otelcollector/test/README.md`
4. Add label to `.github/pull_request_template.md`
5. Add test suite to `otelcollector/test/testkube/testkube-test-crs.yaml`
6. If new scrape jobs needed, add to `otelcollector/test/test-cluster-yamls/`
7. If new API server permissions needed, update `otelcollector/test/testkube/api-server-permissions.yaml`
