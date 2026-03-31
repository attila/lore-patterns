---
title: YAML strings should not be quoted unless necessary
description: Never enclose YAML string values in quotes unless required for YAML parsing – applies to all authored yml/yaml files
tags: [ yaml, yml, formatting, quotes ]
---

- YAML strings should not be quoted unless necessary
    - Do not quote string values in YAML files unless the value requires quotes for correct YAML parsing (e.g., contains
      special characters, starts with `*`, `&`, `!`, etc.).
    - **Why:** Unnecessary quoting adds visual noise and is not idiomatic YAML. Clean YAML uses bare strings.
    - **How to apply:** When writing any `.yml` or `.yaml` file (moon configs, GitHub Actions, pnpm-workspace, etc.),
      use bare string values.
    - **Exceptions**: generated files like `pnpm-lock.yaml` are not authored by us and don't apply. Only quote when YAML
      parsing would break without them.

Examples:

- Good: `language: typescript`
- Bad: `language: "typescript"`
- Good: `defaultBranch: main`
- Bad: `defaultBranch: "main"`
- OK to quote: `command: "prettier --check '**/*.{md,yaml,yml}'"` (contains special chars)
