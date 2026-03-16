# Fix Critical Vulnerabilities

## Description
Identifies and fixes critical/high vulnerabilities using the repo's own scanning tools (Trivy, Dependabot) across Go modules, npm packages, and container images.

USE FOR: fix critical vulnerability, fix high vulnerability, CVE fix, trivy fix, security vulnerability remediation, patch CVE, dependency vulnerability fix
DO NOT USE FOR: general dependency updates without security motivation, adding new scanning tools, security architecture review, threat modeling

## Instructions

### When to Apply
When Trivy scans, Dependabot alerts, or CI/CD security gates flag critical or high severity vulnerabilities.

### Step-by-Step Procedure

#### 1. Vulnerability Discovery
This repo uses:
- **Trivy** — Container and filesystem vulnerability scanning (`.github/workflows/scan.yml`, `.github/workflows/scan-released-image.yml`)
- **Dependabot** — Go module and GitHub Actions dependency updates (`.github/dependabot.yml`)

Run Trivy locally:
```
trivy fs --severity CRITICAL,HIGH --scanners vuln .
```

For specific Go modules:
```
trivy fs --severity CRITICAL,HIGH --scanners vuln otelcollector/opentelemetry-collector-builder/go.mod
```

#### 2. Vulnerability Triage
- **Direct dependencies:** Check if the package is in `go.mod` `require` (not `// indirect`). Priority: HIGH.
- **Transitive dependencies:** `// indirect` entries in `go.mod`. May require bumping the parent dependency.
- **Container base image:** Check `otelcollector/build/linux/Dockerfile` and `otelcollector/build/windows/Dockerfile` for base image versions.
- **Already-ignored:** Check `.trivyignore` — if CVE is listed with justification, skip it.

#### 3. Fix Implementation

**Go module vulnerabilities:**
1. Update the package: `go get <package>@<fixed-version>` in the affected module directory
2. Run `go mod tidy`
3. Check ALL 24 Go modules — the same dependency may appear in multiple `go.mod` files
4. Verify: `go mod graph | grep <vulnerable-package>`

**npm vulnerabilities:**
1. `cd tools/az-prom-rules-converter && npm audit fix`
2. For remaining issues: manually update `package.json` and `npm install`

**Container base image vulnerabilities:**
1. Update the `FROM` line version/digest in Dockerfile(s)
2. Rebuild and re-scan

**Unfixable vulnerabilities:**
1. Add to `.trivyignore` with CVE ID, date, and justification
2. Follow existing format in `.trivyignore`

#### 4. Build and Test
1. Build: `cd otelcollector/opentelemetry-collector-builder && make all`
2. TypeScript: `cd tools/az-prom-rules-converter && npm test`
3. Re-scan: `trivy fs --severity CRITICAL,HIGH --scanners vuln .`
4. Verify targeted CVEs are resolved and no new critical/high CVEs introduced

#### 5. Commit
- Single CVE: `fix: patch CVE-YYYY-NNNNN in <package>`
- Multiple CVEs: `fix: remediate critical/high vulnerabilities in <component>`

### Files Typically Involved
- `otelcollector/*/go.mod`, `otelcollector/*/go.sum`
- `otelcollector/test/ginkgo-e2e/*/go.mod`
- `tools/az-prom-rules-converter/package.json`
- `otelcollector/build/linux/Dockerfile`, `otelcollector/build/windows/Dockerfile`
- `.trivyignore`
- `.github/workflows/scan.yml`, `.github/workflows/scan-released-image.yml`

### Validation
- Build succeeds for all affected components
- All existing tests pass
- Re-scan shows targeted CVEs are resolved
- No new critical/high vulnerabilities introduced
- `.trivyignore` entries have proper justification

## Examples from This Repo
- `Upgrade ksm for CVE fixes (#1355)`
- `.trivyignore` contains `CVE-2026-24051` with justification
