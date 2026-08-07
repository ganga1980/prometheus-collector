# Documentation

## Description
Guides documentation updates including release notes, READMEs, and internal docs.

USE FOR: update docs, update README, release notes, changelog, add documentation
DO NOT USE FOR: code comments, inline documentation changes, API reference generation

## Instructions

### When to Apply
When updating release notes, documentation files, or adding new documentation for features.

### Step-by-Step Procedure
1. Identify which documentation file needs updating
2. For releases: update `RELEASENOTES.md` or `REMOTE-WRITE-RELEASENOTES.md`
3. Follow existing documentation format and style (ATX headings, inline code blocks)
4. Verify all file paths referenced in documentation actually exist
5. Commit with descriptive message: `update version and release notes for <month> release`

### Files Typically Involved
- `RELEASENOTES.md` — main release notes
- `REMOTE-WRITE-RELEASENOTES.md` — remote write specific release notes
- `README.md` — repo overview
- `otelcollector/test/README.md` — test documentation
- Template `README.md` files in `AddonArmTemplate/`, `AddonBicepTemplate/`, etc.

### Validation
- All referenced file paths exist
- Links are valid
- Formatting is consistent with existing documentation

## Examples from This Repo
- `update version and release notes for November release (#1343)`
- `update image in release notes (#1402)`
- `Fix doc for backdoor testing (#1361)`
