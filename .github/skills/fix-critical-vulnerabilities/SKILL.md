# Fix Critical Vulnerabilities

## Description
Identify and fix critical/high vulnerabilities using the repo's own scanning tools (Trivy, Dependabot).

USE FOR: fix critical vulnerability, fix high vulnerability, CVE fix, trivy fix, security vulnerability remediation, patch CVE, dependency vulnerability fix
DO NOT USE FOR: general dependency updates without security motivation, adding new scanning tools, threat modeling (use security-review skill)

## Instructions

### When to Apply
When Trivy, Dependabot, or CI security scans report critical or high severity vulnerabilities.

### Step-by-Step Procedure

#### 1. Vulnerability Discovery
Run the repo's scanning tools:
```bash
# Scan Go modules
trivy fs --severity CRITICAL,HIGH --scanners vuln otelcollector/opentelemetry-collector-builder/

# Scan container images (if built locally)
trivy image --severity CRITICAL,HIGH <image-name>

# Check Go vulnerabilities
cd otelcollector/opentelemetry-collector-builder && govulncheck ./...
```

#### 2. Vulnerability Triage
- **Direct dependencies:** Listed in `go.mod` `require` (not `// indirect`) → HIGH priority
- **Transitive dependencies:** `// indirect` in `go.mod` → bump the parent dependency
- **Base image vulnerabilities:** Check Dockerfile `FROM` lines for newer tags
- **Already-ignored:** Check `.trivyignore` — skip if justified, flag if unjustified

#### 3. Fix Implementation

**Go module vulnerabilities:**
1. `go get <package>@<fixed-version>` in the affected module directory
2. `go mod tidy`
3. If multiple `go.mod` files exist, update ALL affected modules
4. Verify: `go mod graph | grep <vulnerable-package>` (old version gone)

**npm vulnerabilities:**
1. `cd tools/az-prom-rules-converter && npm audit fix`
2. For remaining: manually update `package.json` and `npm install`

**Container base image vulnerabilities:**
1. Check for newer base image tag in Dockerfile
2. Update `FROM` line in `otelcollector/build/linux/Dockerfile` and/or `otelcollector/build/windows/Dockerfile`
3. Rebuild and re-scan

**Unfixable vulnerabilities:**
1. Add to `.trivyignore` with justification:
   ```
   # CVE-YYYY-NNNNN: No fix available upstream as of YYYY-MM-DD
   CVE-YYYY-NNNNN
   ```

#### 4. Build and Test
1. Build: `cd otelcollector/opentelemetry-collector-builder && make`
2. Test: `go test ./...` in affected modules
3. Re-scan: Run same Trivy command — verify CVE is resolved
4. Confirm no NEW critical/high CVEs introduced

### Files Typically Involved
- `otelcollector/*/go.mod`, `otelcollector/*/go.sum`
- `tools/az-prom-rules-converter/package.json`, `package-lock.json`
- `otelcollector/build/linux/Dockerfile`, `otelcollector/build/windows/Dockerfile`
- `.trivyignore`
- `.github/workflows/scan.yml`, `.github/workflows/scan-released-image.yml`

### Validation
- Build succeeds for all affected components
- All existing tests pass
- Re-scan shows targeted CVEs resolved
- No new critical/high CVEs introduced
- `.trivyignore` entries (if any) have justification
- Commit message: `fix: patch CVE-YYYY-NNNNN in <package>`

## Examples from This Repo
- `Upgrade ksm for CVE fixes (#1355)`
- `.trivyignore` contains `CVE-2026-24051` with justification

## References
- `.github/workflows/scan.yml` — Trivy scanning workflow
- `.github/workflows/scan-released-image.yml` — Post-release scanning
- `.trivyignore` — CVE exemption list
- `.github/dependabot.yml` — Automated dependency updates
