# Test Framework Guide

## Test Decision Tree

When adding tests, use this decision tree:

1. **Pure Go logic with no external dependencies?** → Unit test (`go test`)
2. **Testing Kubernetes integration or scrape behavior?** → Ginkgo E2E test (`otelcollector/test/ginkgo-e2e/`)
3. **Testing TypeScript rules converter?** → Jest test (`tools/az-prom-rules-converter/`)
4. **Testing configuration parsing?** → Unit test in `configmapparser/` or `shared/`
5. **Testing Prometheus receiver behavior?** → Unit test in `prometheusreceiver/`

## Test Patterns in This Repo

### Unit Tests
- **Framework**: Standard Go `testing` package
- **Location**: `*_test.go` files alongside source code
- **Naming**: `func Test<FunctionName>(t *testing.T)`
- **Run**: `cd otelcollector && go test ./...`

### Ginkgo E2E Tests (Primary Integration Tests)
- **Framework**: Ginkgo v2 + Gomega matchers
- **Location**: `otelcollector/test/ginkgo-e2e/` with 8+ test suites
- **Structure**: `Describe`/`Context`/`It` blocks with label-based filtering
- **Labels**: `operator`, `windows`, `arm64`, `arc-extension`, `fips`
- **Run**: `cd otelcollector/test/ginkgo-e2e/<suite> && go test -v ./... -ginkgo.label-filter="<label>"`
- **Prerequisites**: Bootstrapped Kubernetes cluster (see `otelcollector/test/README.md`)

### TypeScript Tests
- **Framework**: Jest with ts-jest
- **Location**: `tools/az-prom-rules-converter/` with `.test.ts` suffix
- **Run**: `cd tools/az-prom-rules-converter && npm test`

## Common Test Utilities

- `otelcollector/test/ginkgo-e2e/utils/setup_utils.go` — `SetupKubernetesClient()`, `ParseK8sYaml()`
- `otelcollector/test/ginkgo-e2e/utils/constants.go` — Test label constants (`OperatorLabel`, `ArcExtensionLabel`, `FIPSLabel`)
- `otelcollector/test/ginkgo-e2e/utils/` — AMW query utilities for Azure Monitor validation

## Test Data

- **Cluster configs**: `otelcollector/test/test-cluster-yamls/` — ConfigMaps, custom resources for E2E tests
- **Testkube CRs**: `otelcollector/test/testkube/testkube-test-crs.yaml` — Test execution definitions
- **API permissions**: `otelcollector/test/testkube/api-server-permissions.yaml` — RBAC for test execution

## Adding New Tests

1. Create test file following the naming convention for the test type.
2. For new E2E test labels:
   - Add constant in `otelcollector/test/utils/constants.go`
   - Document in `otelcollector/test/README.md`
   - Add to `.github/pull_request_template.md` checklist
   - Add to `otelcollector/test/testkube/testkube-test-crs.yaml`
3. For new E2E scrape jobs: add to `otelcollector/test/test-cluster-yamls/`
4. For new API permissions: update `otelcollector/test/testkube/api-server-permissions.yaml`
