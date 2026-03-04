# Coding Agent Instructions

This document explains how to use the AI coding agent artifacts generated for this repository. These artifacts make AI assistants (GitHub Copilot, Google Jules, Gemini CLI, Cursor, etc.) understand the prometheus-collector codebase deeply and contribute effectively.

## Quick Start

1. Open this repository in VS Code (or your preferred editor with Copilot/AI assistant support).
2. The AI assistant automatically loads `copilot-instructions.md` on every session — no action needed.
3. When you open a file matching a language pattern, the corresponding `.instructions.md` file auto-activates.
4. Invoke skills by typing their trigger phrases in chat (e.g., "add test", "fix bug", "security review").
5. Invoke agents by @-mentioning them in chat (e.g., `@CodeReviewer`, `@DocumentWriter`).

## Generated Artifacts Overview

| Artifact | Path | Loaded | Purpose |
|----------|------|--------|---------|
| `copilot-instructions.md` | `.github/copilot-instructions.md` | Automatically every session | Root router — general rules, build instructions, skill catalogue |
| `AGENTS.md` | Root | Automatically (supported tools) | Setup commands, code style, testing instructions, dev environment tips |
| Go instructions | `.github/instructions/go.instructions.md` | Auto on `**/*.go` files | Go coding conventions for this repo |
| Shell instructions | `.github/instructions/shell.instructions.md` | Auto on `**/*.sh` files | Shell script conventions |
| TypeScript instructions | `.github/instructions/typescript.instructions.md` | Auto on `tools/az-prom-rules-converter/**/*.ts` | TypeScript tool conventions |
| `Prompt.md` | Root | On demand | Reusable task-spec template for describing new work |
| Skill files (`SKILL.md`) | `.agents/skills/<name>/SKILL.md` | On keyword trigger | Step-by-step guides for recurring development tasks |
| `CodeReviewer.agent.md` | `.github/agents/CodeReviewer.agent.md` | On @-mention | Structured code review following repo conventions |
| `DocumentWriter.agent.md` | `.github/agents/DocumentWriter.agent.md` | On @-mention | Documentation authoring following repo doc standards |
| `prd.agent.md` | `.github/agents/prd.agent.md` | On @-mention | PRD generation tailored to this project's architecture |
| Test AGENTS.md | `otelcollector/test/AGENTS.md` | Automatically in test directory | Test framework guide, decision tree, patterns |
| `.vscode/mcp.json` | `.vscode/mcp.json` | Automatically by VS Code | MCP server configuration (starter) |

## How the Context Loading Chain Works

```
Layer 1: copilot-instructions.md (always loaded)
  ├── General rules, build instructions, skill catalogue
  ├── Routes to →
  │
Layer 2: .instructions.md files (auto-loaded when you open matching files)
  ├── go.instructions.md → activated on *.go files
  ├── shell.instructions.md → activated on *.sh files
  ├── typescript.instructions.md → activated on TS files in tools/
  │
Layer 3: Skills (loaded only when invoked by trigger phrase)
  └── Step-by-step procedures for specific tasks
```

**You don't need to manually load anything.** The system activates automatically based on what file you're editing and what you ask the AI to do.

## Using Custom Agents

### @CodeReviewer
- **Invoke:** Type `@CodeReviewer` in Copilot Chat.
- **What it does:** Performs structured code reviews covering correctness, style, security (STRIDE), telemetry gaps, and adherence to project conventions.
- **Example prompts:**
  - `@CodeReviewer review this PR`
  - `@CodeReviewer check this file for security issues`
  - `@CodeReviewer review my changes for telemetry gaps`
- **What it checks:** Go naming conventions, error handling, secrets/credentials, import ordering, STRIDE security, telemetry instrumentation.

### @DocumentWriter
- **Invoke:** Type `@DocumentWriter` in Copilot Chat.
- **What it does:** Creates and maintains documentation following this repo's conventions — tables, code blocks, relative links.
- **Example prompts:**
  - `@DocumentWriter write a README for this module`
  - `@DocumentWriter update the release notes`
  - `@DocumentWriter generate setup instructions`

### @prd (PRD Generator)
- **Invoke:** Type `@prd` in Copilot Chat.
- **What it does:** Generates structured Product Requirements Documents tailored to the prometheus-collector architecture.
- **Example prompts:**
  - `@prd create a PRD for adding a new scrape target`
  - `@prd write requirements for Windows arm64 support`

## Using Skills

Skills are step-by-step guides that activate when you use their trigger phrases in chat.

### Always-Available Skills

| Skill | Trigger Phrases | What It Does |
|-------|----------------|--------------|
| `security-review` | "security review", "STRIDE analysis", "credential check" | STRIDE-based security review, credential scanning, weak pattern detection |
| `telemetry-authoring` | "add telemetry", "add metrics", "instrument code" | Add Application Insights telemetry following existing patterns |
| `fix-critical-vulnerabilities` | "fix CVE", "trivy fix", "patch vulnerability" | Fix critical/high CVEs using Trivy and repo scanning tools |

### Commit-History-Driven Skills

| Skill | Trigger Phrases | What It Does |
|-------|----------------|--------------|
| `dependency-update` | "update dependency", "bump package", "dependabot" | Safe Go module and npm dependency updates across all modules |
| `test-authoring` | "add test", "write test", "test coverage" | Write Ginkgo E2E or Go unit tests following repo patterns |
| `bug-fix` | "fix bug", "resolve issue", "hotfix" | Structured bug-fix workflow with regression test |
| `feature-development` | "add feature", "implement", "new scrape target" | New feature scaffolding with proper file placement |
| `ci-cd-pipeline` | "update pipeline", "CI change", "fix build" | Modify Azure Pipelines or GitHub Actions workflows |

**Example usage:**
```
# In Copilot Chat, just describe the task naturally:
"Add a Ginkgo test for the new DCGM scrape target"
"Fix the critical CVE in our container base image"
"Add Application Insights telemetry to the config reader"
"Bump the OpenTelemetry Collector to the latest version"
```

## Using Prompt.md for New Work

`Prompt.md` is a reusable template for describing new tasks or features. Use it when:
- Starting a new feature and want to give the AI full context
- Handing off a task specification to another developer or AI agent
- Creating a structured brief for a complex change

**How to use:**
1. Copy `Prompt.md` to a new file (e.g., `feature-gpu-metrics-prompt.md`).
2. Fill in the sections with your specific requirements.
3. Reference it in Copilot Chat: "Implement the feature described in feature-gpu-metrics-prompt.md".

## Using AGENTS.md

`AGENTS.md` provides setup, style, and testing instructions. Most AI tools load it automatically. It's useful for:
- **Onboarding:** Follow the Setup Commands to get a working dev environment.
- **Consistency:** Code Style and Testing Instructions ensure AI-generated code matches repo conventions.
- **PR readiness:** PR Instructions help format commits and PRs correctly.

## Tips for Maximum Productivity

1. **Let auto-loading work for you** — Just open the file you're working on. The `.instructions.md` files activate automatically.
2. **Use natural language for skills** — Just describe the task: "add a test", "bump dependencies", "review security".
3. **Start reviews with @CodeReviewer** — It knows the repo's review patterns, linter rules, and security requirements.
4. **Use @prd before big features** — A structured PRD helps understand scope before writing code.
5. **Check the test AGENTS.md** — `otelcollector/test/AGENTS.md` has a decision tree for choosing the right test type.
6. **Multiple go.mod files** — This repo has 15+ Go modules. Skills like `dependency-update` know all the paths.
7. **Check AGENTS.md for setup** — If builds fail, verify the Setup Commands match your Go version.

## Customizing These Artifacts

These files are meant to evolve with your project:
- **Add rules** to `.instructions.md` files when you establish new coding conventions.
- **Add skills** when you identify a new recurring workflow (create a `SKILL.md` in `.agents/skills/<name>/`).
- **Update `copilot-instructions.md`** when project structure or build commands change.
- **Update `AGENTS.md`** when setup commands or test strategies change.
- **Re-run generation** periodically to pick up new commit patterns and refresh skills.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| AI doesn't follow Go conventions | Verify `.github/instructions/go.instructions.md` exists and `applyTo` matches `**/*.go` |
| Skill not activating | Use the exact trigger phrases listed in the skill table above |
| Agent not available | Ensure the `.agent.md` file is in `.github/agents/` |
| Build commands fail | Update Setup Commands in `AGENTS.md` to match your Go version |
| AI gives generic advice | It may not be loading `copilot-instructions.md` — verify it's at `.github/copilot-instructions.md` |
| Wrong go.mod updated | This repo has 15+ modules — check the `dependency-update` skill for the full list |
