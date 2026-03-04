---
applyTo: "tools/az-prom-rules-converter/**/*.ts,tools/az-prom-rules-converter/**/*.js"
description: "TypeScript conventions for the az-prom-rules-converter tool"
---

# TypeScript Conventions (az-prom-rules-converter)

1. **Build**: Compile with `tsc` via `npm run build`. Output goes to `dist/`.
2. **Testing**: Use Jest (`npm test`). Test files follow Jest conventions.
3. **CLI framework**: Use `commander` for CLI argument parsing.
4. **YAML handling**: Use `js-yaml` for parsing and serializing YAML files.
5. **Validation**: Use `ajv` with `ajv-formats` for JSON schema validation. Schemas are in `src/schemas/`.
6. **Module structure**: Steps go in `src/steps/`, utilities in `src/utils/`, types in `src/types/`.
7. **Dependencies**: Keep `devDependencies` separate from `dependencies` in `package.json`.
