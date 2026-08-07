# Security Review

## Description
STRIDE-based security review for code changes in the prometheus-collector.

USE FOR: security review, threat model, STRIDE analysis, credential leak check, secret scan, vulnerability review, security audit
DO NOT USE FOR: performance optimization, functional bug fixes, code style issues

## Instructions

### When to Apply
Apply to any PR that modifies authentication, network-facing code, data handling, Dockerfiles, Helm charts, or dependency changes.

### Step-by-Step Procedure

#### 1. STRIDE Threat Model Checklist

**Spoofing (Identity)**
- Authentication checks present at all entry points
- Service-to-service calls use managed identity or mTLS
- Token validation in Kubernetes ServiceAccount bindings

**Tampering (Data Integrity)**
- Input validated at trust boundaries (config parsing, API endpoints)
- File permissions restrictive in Dockerfiles (no world-writable)
- Helm chart values validated against schema

**Repudiation (Auditability)**
- Security-relevant actions logged (auth attempts, config changes)
- Logs include context (who, what, when) without sensitive data

**Information Disclosure (Confidentiality)**
- No hardcoded secrets, API keys, tokens, or connection strings
- Secrets loaded via environment variables or Kubernetes Secrets
- No secrets in log output, error messages, or telemetry
- `.trivyignore` entries have justifications

**Denial of Service (Availability)**
- Resource limits set in Kubernetes manifests (CPU, memory)
- Timeouts configured for external calls
- Container resource limits in Helm values

**Elevation of Privilege (Authorization)**
- Containers run as non-root (USER directive in Dockerfile)
- RBAC ClusterRole uses least-privilege (no wildcard verbs)
- Security contexts set (readOnlyRootFilesystem, drop capabilities)

#### 2. Credential & Secret Leak Detection
- Scan for hardcoded strings matching: API keys, connection strings, tokens, private keys
- Verify `.gitignore` excludes `*.pem`, `*.key`, `.env`
- Check `APPLICATIONINSIGHTS_AUTH_*` values are base64-encoded instrumentation keys (not secrets)

#### 3. Weak Security Patterns

**Go:**
- No `#nosec` without justification
- No `fmt.Sprintf` for query construction
- No `exec.Command` with unsanitized input
- Errors from crypto/auth functions always checked

**Shell:**
- Variables quoted in commands
- No `chmod 777` or overly permissive permissions
- No secrets as CLI arguments

**Infrastructure:**
- Containers not running as root without justification
- No `latest` tags in Dockerfiles (pin versions)
- No `privileged: true` in Kubernetes manifests
- Security contexts set (runAsNonRoot, readOnlyRootFilesystem)

### Validation
- Run `trivy fs --severity CRITICAL,HIGH --scanners vuln .` on changed modules
- Verify `.trivyignore` entries have justifications
- Confirm `SECURITY.md` exists with responsible disclosure info

## Examples from This Repo
- `Upgrade ksm for CVE fixes (#1355)` — CVE remediation
- `.trivyignore` — Documented security scan exemptions

## References
- `SECURITY.md` — Microsoft security vulnerability reporting
- `.github/dependabot.yml` — Automated dependency scanning
- `.trivyignore` — CVE exemption list
