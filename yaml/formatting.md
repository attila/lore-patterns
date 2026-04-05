---
tags: [yaml, yml, formatting, quotes]
---

# YAML Formatting

## Never quote strings unless required by the parser

Do not quote string values in YAML files unless the value requires quotes for
correct YAML parsing (e.g., contains special characters, starts with `*`, `&`,
`!`, etc.).

**Why:** Unnecessary quoting adds visual noise and is not idiomatic YAML. Clean
YAML uses bare strings.

**How to apply:** When writing or editing any `.yml` or `.yaml` file (CI
workflows, moon configurations, pnpm-workspace, etc.), use bare string values.
Generated files like `pnpm-lock.yaml` are not authored by us and do not apply.
Only quote when YAML parsing would break without them.

Examples:

- Good: `language: typescript`
- Bad: `language: "typescript"`
- Good: `defaultBranch: main`
- Bad: `defaultBranch: "main"`
- OK to quote: `command: "prettier --check '**/*.{md,yaml,yml}'"` (contains
  special characters)
