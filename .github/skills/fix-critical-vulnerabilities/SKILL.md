# Fix Critical Vulnerabilities

## Description
Identifies and fixes critical/high vulnerabilities using the repository's own scanning tools (Trivy, Dependabot).

USE FOR: fix critical vulnerability, fix high vulnerability, CVE fix, trivy fix, security vulnerability remediation, patch CVE, fix security scan failure, dependency vulnerability fix, container image vulnerability
DO NOT USE FOR: general dependency updates without security motivation, adding new security tools, security architecture review (use security-review), low/medium severity unless explicitly requested

## Instructions

### 1. Vulnerability Discovery

**Detect repo scanning tools** (from CI/CD configs):
- **Trivy**: `.github/workflows/scan.yml` and `.github/workflows/scan-released-image.yml`
- **Dependabot**: `.github/dependabot.yml` (daily Go module and GitHub Actions updates)
- **Trivy ignore file**: `.trivyignore`

**Run scans locally:**
```bash
# Filesystem scan for Go dependencies
trivy fs --severity CRITICAL,HIGH --scanners vuln otelcollector/

# Container image scan (if image is built locally)
trivy image --severity CRITICAL,HIGH <image-name>

# Go vulnerability check
cd otelcollector && govulncheck ./...

# Check npm vulnerabilities
cd tools/az-prom-rules-converter && npm audit --audit-level=high
```

### 2. Vulnerability Triage

**a. Direct dependencies** — In `go.mod` `require` blocks (not `// indirect`). Priority: HIGH.
**b. Transitive dependencies** — `// indirect` entries. Priority: MEDIUM — bump the direct parent.
**c. OS/base image vulnerabilities** — Check Dockerfile base images for updates:
  - `mcr.microsoft.com/azurelinux/distroless/base:3.0`
  - `mcr.microsoft.com/oss/go/microsoft/golang:1.23.x`
  - `mcr.microsoft.com/windows/servercore:ltsc2022`
**d. Already-ignored** — Check `.trivyignore` for existing entries with justification.

### 3. Fix Implementation

**Go module vulnerabilities:**
```bash
cd otelcollector
go get <package>@<fixed-version>
go mod tidy
# Check ALL 24 go.mod files for the same vulnerability
find .. -name 'go.mod' -exec grep -l '<vulnerable-package>' {} \;
```

**Container base image vulnerabilities:**
- Update `FROM` line in affected Dockerfiles (`otelcollector/build/linux/Dockerfile`, etc.)
- Rebuild and re-scan to verify.

**Unfixable vulnerabilities:**
- Add to `.trivyignore` with: CVE ID, date, reason, upstream issue link.

### 4. Build and Test
```bash
cd otelcollector && go build ./...
cd otelcollector && go test ./...
# Re-scan to verify fix
trivy fs --severity CRITICAL,HIGH --scanners vuln otelcollector/
```

### 5. Commit
- Single CVE: `fix: patch CVE-YYYY-NNNNN in <package>`
- Multiple: `fix: remediate critical/high vulnerabilities in <component>`

### Files Typically Involved
- `otelcollector/go.mod`, `otelcollector/go.sum`
- Other `go.mod` files (24 total across repo)
- `otelcollector/build/linux/Dockerfile`, `otelcollector/build/windows/Dockerfile`
- `.trivyignore`
- `.github/workflows/scan.yml`

### Validation
- Build succeeds for all affected components
- All tests pass
- Re-scan shows targeted CVEs resolved
- No new critical/high vulnerabilities introduced
- `.trivyignore` entries have proper justification
