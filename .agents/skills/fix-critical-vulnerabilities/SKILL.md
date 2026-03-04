# Fix Critical Vulnerabilities

## Description
Identifies and fixes critical/high severity vulnerabilities using Trivy and the repo's dependency management tools.

USE FOR: fix critical vulnerability, fix high vulnerability, CVE fix, trivy fix, security vulnerability remediation, patch CVE, fix security scan failure, resolve critical CVE, dependency vulnerability fix, container image vulnerability
DO NOT USE FOR: general dependency updates without security motivation, adding new security scanning tools, security architecture review (use security-review), threat modeling, low/medium severity vulnerabilities unless explicitly requested

## Instructions

### When to Apply
When Trivy or Dependabot reports critical/high CVEs in Go modules, container base images, or OS packages.

### Step-by-Step Procedure

#### 1. Vulnerability Discovery
This repo uses these scanning tools:
- **Trivy**: Container image and filesystem scanning (`.github/workflows/scan.yml`, `.pipelines/azure-pipeline-build.yml`)
- **CodeQL**: SAST in Azure Pipelines (`Codeql.Enabled: true`)
- **Dependabot**: Go module scanning (`.github/dependabot.yml`)

Run locally:
```bash
# Scan the filesystem for vulnerable dependencies
trivy fs --severity CRITICAL,HIGH --scanners vuln .

# Scan a specific Go module
trivy fs --severity CRITICAL,HIGH --scanners vuln otelcollector/opentelemetry-collector-builder/

# Scan the container image (if built locally)
trivy image --severity CRITICAL,HIGH <image-tag>
```

#### 2. Vulnerability Triage
- **Direct dependency**: Package in `go.mod` `require` block → HIGH priority, directly fixable
- **Transitive dependency**: `// indirect` in `go.mod` → bump the parent dependency
- **OS/base image**: Container base image vulnerability → update Dockerfile `FROM` line
- **Already ignored**: Check `.trivyignore` — if CVE listed with justification, skip

#### 3. Fix Implementation

**Go module vulnerabilities:**
```bash
cd otelcollector/<component>
go get <package>@<fixed-version>
go mod tidy
```
Check ALL `go.mod` locations (see `dependency-update` skill for full list).
Verify with: `go mod graph | grep <vulnerable-package>`

**Container base image vulnerabilities:**
- Update the `FROM` line in `otelcollector/build/linux/Dockerfile` or `otelcollector/build/windows/Dockerfile`
- For Mariner packages: update version in `otelcollector/scripts/setup.sh` (e.g., `tdnf install -y metricsext2-<version>`)

**OS package vulnerabilities:**
- Update package version in setup scripts
- If no fix available, add to `.trivyignore` with justification and date

**Unfixable vulnerabilities:**
Add to `.trivyignore`:
```
# CVE-YYYY-NNNNN: No fix available upstream as of YYYY-MM-DD
# Package: <name>, Affected version: <version>
CVE-YYYY-NNNNN
```

#### 4. Build and Test
```bash
# Build affected components
cd otelcollector/opentelemetry-collector-builder && make
cd otelcollector/prom-config-validator-builder && make
cd otelcollector/fluent-bit/src && make

# Run unit tests
cd otelcollector/prometheusreceiver && go test ./...
cd otelcollector/shared && go test ./...

# Re-scan to verify fix
trivy fs --severity CRITICAL,HIGH --scanners vuln .
```

#### 5. Commit and Document
- **Commit format**: `fix: patch CVE-YYYY-NNNNN in <package>` or `fix: remediate critical/high vulnerabilities in <component>`
- **PR description**: Table of CVEs fixed (ID, severity, package, old→new version)
- **Ignore file updates**: Include justification comments in `.trivyignore`

### Files Typically Involved
- `otelcollector/*/go.mod`, `otelcollector/*/go.sum`
- `otelcollector/build/linux/Dockerfile`, `otelcollector/build/windows/Dockerfile`
- `otelcollector/scripts/setup.sh` (OS package versions)
- `.trivyignore`
- `.pipelines/azure-pipeline-build.yml` (scan configuration)

### Validation
- Build succeeds for all affected components
- All existing tests pass
- Re-scan shows targeted CVEs are resolved
- No new critical/high vulnerabilities introduced
- `.trivyignore` entries have proper justification

## Examples from This Repo
- `Upgrade ksm for CVE fixes (#1355)` (7022a7c)
- `build(deps): bump github.com/golang-jwt/jwt/v5 from 5.2.1 to 5.2.2` (3c7c69a)
- `build(deps): bump github.com/docker/docker from 28.3.0 to 28.3.3` (1f0886f)

## References
- `.trivyignore` — current CVE exclusions
- `.github/dependabot.yml` — automated dependency scanning config
- `.github/workflows/scan.yml` — Trivy scan workflow
- `.pipelines/azure-pipeline-build.yml` — CI scan integration
