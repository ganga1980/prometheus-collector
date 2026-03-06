# Coding Agent Instructions

This document explains how to use the AI coding agent artifacts generated for this repository. These artifacts make AI assistants (GitHub Copilot, Google Jules, Gemini CLI, Cursor, etc.) understand your codebase deeply and contribute effectively.

## Quick Start

1. Open this repository in VS Code (or your preferred editor with Copilot/AI assistant support).
2. The AI assistant automatically loads `copilot-instructions.md` on every session — no action needed.
3. When you open a file matching a language pattern, the corresponding `.instructions.md` file auto-activates.
4. Invoke skills by typing their trigger phrases in chat (e.g., "add test", "fix bug", "security review").
5. Invoke agents by @-mentioning them in chat (e.g., `@CodeReviewer`, `@DocumentWriter`).

## Generated Artifacts Overview

| Artifact | Path | Loaded | Purpose |
|----------|------|--------|---------|
| `copilot-instructions.md` | `.github/copilot-instructions.md` | Automatically every session | Root router — architecture, build commands, skill catalogue |
| `AGENTS.md` | Root | Automatically (supported tools) | Setup commands, code style, testing instructions, dev tips |
| `.instructions.md` files | `.github/instructions/` | Auto on file match (`applyTo` glob) | Language-specific coding rules (Go, Shell, TypeScript) |
| `Prompt.md` | Root | On demand | Reusable task-spec template for describing new work |
| Skill files (`SKILL.md`) | `.github/skills/*/` | On keyword trigger | Step-by-step guides for recurring development tasks |
| `CodeReviewer.agent.md` | `.github/agents/` | On @-mention | Structured code review following repo conventions |
| `SecurityReviewer.agent.md` | `.github/agents/` | On @-mention | Deep security analysis, threat modeling, attack surface review |
| `ThreatModelAnalyst.agent.md` | `.github/agents/` | On @-mention | STRIDE threat modeling with Mermaid diagrams under `threat-model/` |
| `DocumentWriter.agent.md` | `.github/agents/` | On @-mention | Documentation authoring following repo doc standards |
| `prd.agent.md` | `.github/agents/` | On @-mention | PRD generation tailored to this project's architecture |
| `.vscode/mcp.json` | `.vscode/mcp.json` | Automatically by VS Code | MCP server connections (GitHub, Microsoft Docs) |
| Test `AGENTS.md` | `otelcollector/test/AGENTS.md` | Automatically in test dirs | Test framework guide, decision tree, Ginkgo patterns |

## How the Context Loading Chain Works

```
Layer 1: copilot-instructions.md (always loaded)
  ├── Architecture overview, build commands, skill catalogue
  ├── Routes to →
  │
Layer 2: .instructions.md files (auto-loaded when you open matching files)
  ├── go.instructions.md — Go conventions (*.go files)
  ├── shell.instructions.md — Shell conventions (*.sh files)
  ├── typescript.instructions.md — TS conventions (*.ts files)
  │
Layer 3: Skills (loaded only when invoked by trigger phrase)
  └── Step-by-step procedures for specific tasks
```

**You don't need to manually load anything.** The system activates automatically based on what file you're editing and what you ask the AI to do.

## Using Custom Agents

### @CodeReviewer
- **Invoke:** Type `@CodeReviewer` in Copilot Chat.
- **What it does:** Performs structured code reviews covering correctness, style, security (STRIDE), telemetry gaps, dependency compatibility, and multi-platform correctness.
- **Example prompts:**
  - `@CodeReviewer review this PR`
  - `@CodeReviewer check this file for security issues`
  - `@CodeReviewer review my changes for telemetry gaps`

### @SecurityReviewer
- **Invoke:** Type `@SecurityReviewer` in Copilot Chat.
- **What it does:** Performs deep security assessments including STRIDE analysis, attack surface enumeration, dependency security auditing, and infrastructure security review specific to the Kubernetes/Azure architecture.
- **Example prompts:**
  - `@SecurityReviewer perform a threat model for the target allocator`
  - `@SecurityReviewer review the TLS certificate handling changes`
  - `@SecurityReviewer audit our container security configuration`
- **When to use vs. @CodeReviewer:** CodeReviewer applies a lightweight STRIDE checklist. Use `@SecurityReviewer` for dedicated deep analysis.

### @ThreatModelAnalyst
- **Invoke:** Type `@ThreatModelAnalyst` in Copilot Chat.
- **What it does:** Generates comprehensive, persistent threat model artifacts — Mermaid architecture diagrams with security boundaries, STRIDE analysis matrices, and threat catalogues. All saved under `threat-model/YYYY-MM-DD/`.
- **Example prompts:**
  - `@ThreatModelAnalyst perform a full threat model analysis`
  - `@ThreatModelAnalyst threat model the OTel Collector scraping pipeline`
  - `@ThreatModelAnalyst refresh the threat model before our quarterly review`

### @DocumentWriter
- **Invoke:** Type `@DocumentWriter` in Copilot Chat.
- **What it does:** Creates and maintains documentation following repo conventions (ATX headings, language-annotated code blocks, relative links).
- **Example prompts:**
  - `@DocumentWriter write a README for this module`
  - `@DocumentWriter update the release notes`

### @prd (PRD Generator)
- **Invoke:** Type `@prd` in Copilot Chat.
- **What it does:** Generates structured Product Requirements Documents tailored to the OTel Collector architecture, Kubernetes deployment model, and Azure Monitor integration.
- **Example prompts:**
  - `@prd create a PRD for adding a new exporter`
  - `@prd write requirements for multi-tenant metric collection`

## Using Skills

Skills are step-by-step guides that activate when you use their trigger phrases in chat.

### Always-Available Skills

| Skill | Trigger Phrases | What It Does |
|-------|----------------|--------------|
| `security-review` | "security review", "STRIDE analysis", "credential leak check" | STRIDE-based security review with credential scanning |
| `telemetry-authoring` | "add telemetry", "add metrics", "instrument code" | Add telemetry following existing AppInsights/OTel patterns |
| `fix-critical-vulnerabilities` | "fix CVE", "trivy fix", "patch vulnerability" | Fix critical/high vulns using Trivy scanning |

### Commit-History-Driven Skills

| Skill | Trigger Phrases | What It Does |
|-------|----------------|--------------|
| `dependency-update` | "update dependency", "bump package" | Safe dependency updates across 24 Go modules |
| `bug-fix` | "fix bug", "resolve issue", "hotfix" | Structured bug fix with regression test |
| `feature-development` | "add feature", "implement", "new exporter" | New feature scaffolding with test and doc requirements |
| `test-authoring` | "add test", "write test", "ginkgo test" | Create Ginkgo E2E or Go unit tests |
| `documentation` | "update docs", "write readme", "release notes" | Documentation following repo conventions |
| `code-refactoring` | "refactor", "restructure", "rename" | Refactoring with behavior preservation |
| `infrastructure` | "update helm", "modify k8s", "change bicep" | IaC changes across Helm/Bicep/Terraform |

## MCP Server Integration

The `.vscode/mcp.json` file configures connections to external data sources:
- **GitHub MCP:** Enables PR creation, issue management, and branch operations directly from chat.
- **Microsoft Docs MCP:** Enables looking up official Azure Monitor, Kubernetes, and OpenTelemetry documentation.

MCP servers use `${input:variable}` prompts — you'll be asked for credentials on first use.

## Tips for Maximum Productivity

1. **Let auto-loading work for you** — Just open the file you're working on. The `.instructions.md` files activate automatically.
2. **Use natural language for skills** — Don't invoke skills by name. Just describe the task: "add a test", "bump dependencies", "review security".
3. **Start reviews with @CodeReviewer** — It knows the repo's review patterns, linter rules, and security requirements.
4. **Use @prd before big features** — A structured PRD helps scope the work across collector components.
5. **Reference Prompt.md for complex tasks** — Copy and fill it in for structured task specifications.
6. **Trust the context chain** — The layered system ensures the AI has the right context at the right time.
7. **Check AGENTS.md for setup** — If the AI struggles with builds, verify the Setup Commands in AGENTS.md.

## Customizing These Artifacts

These files evolve with your project:
- **Add skills** when new repeating patterns emerge (3+ occurrences).
- **Update .instructions.md** when team conventions change.
- **Add agent capabilities** when new MCP servers are configured.
- **Keep copilot-instructions.md as the router** — it should point to deeper docs, not duplicate them.
