# Coding Agent Instructions

This document explains how to use the AI coding agent artifacts generated for the Azure Monitor Prometheus Collector repository. These artifacts make AI assistants (GitHub Copilot, Google Jules, Gemini CLI, Cursor, etc.) understand your codebase deeply and contribute effectively.

## Quick Start

1. Open this repository in VS Code (or your preferred editor with Copilot/AI assistant support).
2. The AI assistant automatically loads `copilot-instructions.md` on every session — no action needed.
3. When you open a `.go` file, `.github/instructions/go.instructions.md` auto-activates.
4. Invoke skills by typing their trigger phrases in chat (e.g., "add test", "fix bug", "security review").
5. Invoke agents by @-mentioning them in chat (e.g., `@CodeReviewer`, `@DocumentWriter`).

## Generated Artifacts Overview

| Artifact | Path | Loaded | Purpose |
|----------|------|--------|---------|
| `copilot-instructions.md` | `.github/copilot-instructions.md` | Automatically every session | Root router — general rules, skill catalogue, build instructions |
| `AGENTS.md` | Root | Automatically (supported tools) | Setup commands, code style, testing instructions, dev environment |
| Go instructions | `.github/instructions/go.instructions.md` | Auto on `**/*.go` | Go coding rules and conventions |
| TypeScript instructions | `.github/instructions/typescript.instructions.md` | Auto on `tools/**/*.ts` | TypeScript tool conventions |
| Shell instructions | `.github/instructions/shell.instructions.md` | Auto on `**/*.sh` | Shell script conventions |
| `Prompt.md` | Root | On demand | Reusable task-spec template |
| Skill files (`SKILL.md`) | `.github/skills/<name>/SKILL.md` | On keyword trigger | Step-by-step guides for recurring tasks |
| `CodeReviewer.agent.md` | `.github/agents/CodeReviewer.agent.md` | On @-mention | Structured code review |
| `SecurityReviewer.agent.md` | `.github/agents/SecurityReviewer.agent.md` | On @-mention | Deep security analysis |
| `ThreatModelAnalyst.agent.md` | `.github/agents/ThreatModelAnalyst.agent.md` | On @-mention | STRIDE threat modeling with Mermaid diagrams |
| `DocumentWriter.agent.md` | `.github/agents/DocumentWriter.agent.md` | On @-mention | Documentation authoring |
| `prd.agent.md` | `.github/agents/prd.agent.md` | On @-mention | PRD generation |
| Test AGENTS.md | `otelcollector/test/ginkgo-e2e/AGENTS.md` | Automatically in test dir | Ginkgo E2E test patterns and decision tree |
| `.vscode/mcp.json` | `.vscode/mcp.json` | Automatically by VS Code | MCP server connections |

## How the Context Loading Chain Works

```
Layer 1: copilot-instructions.md (always loaded)
  ├── General rules, skill catalogue, build instructions
  ├── Routes to →
  │
Layer 2: .instructions.md files (auto-loaded when you open matching files)
  ├── go.instructions.md — Go code conventions
  ├── typescript.instructions.md — TypeScript tool conventions
  ├── shell.instructions.md — Shell script conventions
  │
Layer 3: Skills (loaded only when invoked by trigger phrase)
  └── Step-by-step procedures for dependency updates, testing, etc.
```

## Using Custom Agents

### @CodeReviewer
- **Invoke:** Type `@CodeReviewer` in Copilot Chat.
- **What it does:** Reviews PRs for correctness, style, security (STRIDE), telemetry gaps, and project conventions.
- **Example prompts:**
  - `@CodeReviewer review this PR`
  - `@CodeReviewer check this file for security issues`
  - `@CodeReviewer review my Go changes for error handling`

### @DocumentWriter
- **Invoke:** Type `@DocumentWriter` in Copilot Chat.
- **What it does:** Creates and maintains documentation following repo conventions.
- **Example prompts:**
  - `@DocumentWriter write a README for this new module`
  - `@DocumentWriter update the release notes for January`

### @SecurityReviewer
- **Invoke:** Type `@SecurityReviewer` in Copilot Chat.
- **What it does:** Deep security analysis — threat modeling, attack surface review, STRIDE deep-dive.
- **Example prompts:**
  - `@SecurityReviewer assess the security of the OTel Collector scrape pipeline`
  - `@SecurityReviewer review the Dockerfile security configuration`
- **When to use vs. @CodeReviewer:** Use for dedicated deep security analysis, not routine PR reviews.

### @ThreatModelAnalyst
- **Invoke:** Type `@ThreatModelAnalyst` in Copilot Chat.
- **What it does:** Generates persistent threat model artifacts under `threat-model/YYYY-MM-DD/`.
- **Example prompts:**
  - `@ThreatModelAnalyst perform a full threat model of the collector pipeline`
  - `@ThreatModelAnalyst analyze Kubernetes RBAC and secrets management`

### @prd (PRD Generator)
- **Invoke:** Type `@prd` in Copilot Chat.
- **What it does:** Generates structured PRDs tailored to this project.
- **Example prompts:**
  - `@prd create a PRD for adding a new scrape target type`
  - `@prd write requirements for Windows ARM64 support`

## Using Skills

Skills activate when you use their trigger phrases in chat:

### Always-Available Skills

| Skill | Trigger Phrases | What It Does |
|-------|----------------|--------------|
| `security-review` | "security review", "STRIDE analysis", "credential leak check" | STRIDE-based security review |
| `telemetry-authoring` | "add telemetry", "add metrics", "instrument code" | Add telemetry following App Insights patterns |
| `fix-critical-vulnerabilities` | "fix CVE", "trivy fix", "patch vulnerability" | Fix critical/high CVEs using Trivy |

### Commit-History-Driven Skills

| Skill | Trigger Phrases | What It Does |
|-------|----------------|--------------|
| `dependency-update` | "update dependency", "bump package" | Safe dependency updates across 24 Go modules |
| `feature-development` | "add feature", "implement", "new component" | New feature scaffolding with tests |
| `test-authoring` | "add test", "write test", "add E2E test" | Create Ginkgo or Jest tests |
| `bug-fix` | "fix bug", "resolve issue", "hotfix" | Bug fix with regression test |
| `documentation` | "update docs", "release notes" | Documentation updates |
| `infrastructure` | "update Dockerfile", "helm chart", "bicep" | Infrastructure changes |
| `ci-cd-pipeline` | "update workflow", "pipeline fix" | CI/CD workflow changes |

## Prompt Engineering Best Practices

### Structuring Effective Prompts
1. **Break complex tasks into smaller prompts** — "Explain the Fluent Bit plugin architecture", then "Add error telemetry to the flush function".
2. **Be specific** — "Add error handling to `FLBPluginFlushCtx` in `otelcollector/fluent-bit/src/out_appinsights.go`".
3. **Provide examples** — Show expected metric format or error message structure.
4. **State constraints** — "Using Go 1.24, follow the existing Application Insights telemetry pattern".
5. **Ask for explanations** — "Explain what `SetupTelemetry()` does before I modify it".

### Prompting Anti-Patterns to Avoid
- Vague requests: "Fix this" without specifying what's wrong
- Overloaded prompts: Multiple unrelated changes in one prompt
- Skipping validation: Never accept code without running `make all`

## Choosing the Right Copilot Tool

| Task | Best Tool | Why |
|------|-----------|-----|
| Completing Go code as you type | **Inline suggestions** | Fastest for boilerplate |
| Questions about code, using @agents | **Copilot Chat (IDE)** | Conversational, supports agents |
| Autonomous multi-file tasks | **Copilot CLI** | Terminal-native, supports `/plan` |
| Reviewing code changes | **@CodeReviewer agent** | Structured review against conventions |
| Async work on separate branches | **Coding agent (`/delegate`)** | Doesn't block local work |

## Context Management

1. **Open relevant files** — Open the Go files in `otelcollector/` related to your task.
2. **Close unrelated files** — Too many open tabs dilute AI focus.
3. **Start fresh for new tasks** — New chat session when switching tasks.
4. **Use @-references** — Reference specific files with `#file:path/to/file.go`.

## Recommended Workflow: Explore → Plan → Code → Commit

1. **Explore** — "Read the target allocator code and explain how scrape targets are distributed"
2. **Plan** — "/plan Add support for a new exporter to the collector pipeline"
3. **Code** — "Implement step 1: add the exporter dependency to go.mod"
4. **Test** — "Run `make all` and fix any failures"
5. **Commit** — "Commit with `feat: add new exporter to collector pipeline`"

| Use this workflow for | Skip for |
|----------------------|----------|
| New feature implementations | Quick bug fixes |
| Multi-module dependency changes | Single file edits |
| Architecture changes | Documentation updates |

## Validating AI-Generated Code

1. **Understand before accepting** — Ask "Explain this code" if unclear.
2. **Build:** `cd otelcollector/opentelemetry-collector-builder && make all`
3. **Test:** Run Ginkgo suites for affected components; `npm test` for TypeScript.
4. **Scan:** `trivy fs --severity CRITICAL,HIGH .` for security.
5. **Check patterns:** Compare against existing code in same module.

## Test-Driven Development with AI

1. Write failing Ginkgo tests: "Write a Ginkgo test for the new scrape target configuration"
2. Review tests for edge cases and correctness
3. Implement to pass: "Write the code to make all tests pass"
4. Refactor with green tests

## Codebase Onboarding with AI

- "How does the OTel Collector scrape Prometheus metrics in this repo?"
- "What's the pattern for adding a new Fluent Bit output plugin?"
- "Explain the target allocator's role in sharding scrape targets"
- "Where are the Kubernetes deployment manifests for the collector?"
- "What environment variables does the telemetry setup require?"

## Security When Using AI Assistants

- **Never commit secrets** — Verify no hardcoded keys in AI-generated code.
- **Review all changes** — AI can produce subtly insecure code.
- **Run Trivy** — After changes: `trivy fs --severity CRITICAL,HIGH .`
- **Don't share credentials** — Use env vars, not chat prompts.

## Measuring AI-Assisted Productivity

- **Time from issue to PR** — How quickly tasks move from backlog to PR.
- **Review iterations** — Number of review rounds on AI-assisted PRs.
- **Test coverage** — Whether AI-assisted development maintains coverage.
- **Bug rate** — Track post-merge defects in AI-assisted code.

## Customizing These Artifacts

- **Add rules** to `.instructions.md` files for new conventions.
- **Add skills** for new recurring workflows (create `SKILL.md` in `.github/skills/`).
- **Update `copilot-instructions.md`** when project structure changes.
- **Re-run generation** periodically to refresh skills from commit history.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| AI doesn't follow Go conventions | Verify `go.instructions.md` `applyTo` matches your file |
| Skill not activating | Use exact trigger phrases from the skill table |
| Agent not available | Ensure `.agent.md` is in `.github/agents/` |
| MCP server not connecting | Check `.vscode/mcp.json` and provide token when prompted |
| Build commands fail | Update Setup Commands in `AGENTS.md` |
| Context confused | Start a new chat session |
