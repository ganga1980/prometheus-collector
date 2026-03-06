# Documentation

## Description
Guides documentation updates including release notes, READMEs, and test documentation.

USE FOR: update docs, write readme, changelog, release notes, document feature
DO NOT USE FOR: code changes, test authoring, infrastructure changes

## Instructions

### When to Apply
When updating documentation after releases, feature additions, or process changes.

### Step-by-Step Procedure
1. **Identify the doc type:**
   - **Release notes** → `RELEASENOTES.md` (most frequent doc change).
   - **Remote write notes** → `REMOTE-WRITE-RELEASENOTES.md`.
   - **Main README** → `README.md`.
   - **Test docs** → `otelcollector/test/README.md`.
   - **Contributing guide** → `CONTRIBUTING.md`.
2. **Follow existing format.** Match the heading style, list formatting, and link conventions of the target file.
3. **Use ATX headings** (`#`, `##`, `###`).
4. **Inline code** for file paths, commands, and variable names.
5. **Reference actual paths** — verify every path exists before including it.
6. **Commit** with format: `docs: <description>`

### Files Typically Involved
- `RELEASENOTES.md` — most frequently updated doc (27 changes/year)
- `REMOTE-WRITE-RELEASENOTES.md`
- `README.md`
- `otelcollector/test/README.md`

### Validation
- All referenced file paths exist
- All links are valid
- Markdown renders correctly

## Examples from This Repo
- `690de4b` — docs: update release notes for latest version
- `593e69e` — docs: update test documentation
- `c691095` — docs: update contributing guide
