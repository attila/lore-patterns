---
language: [javascript, typescript]
tags: [testing, conventions, javascript, typescript, vitest, jest]
---

# Test Writing Conventions

Before writing or modifying any test file, run this checklist on every `it()`
block.

## AAA structure with comment labels

Each phase on its own line(s) must be labelled: `// Arrange`, `// Act`,
`// Assert`, or `// Act & Assert`.

- Arrange (optional) → optional blank line → Act → optional blank line → Assert
  - Add blank lines only to longer blocks to help readability.
- Act+Assert may merge for trivial one-liners (no comment needed)

**Why:** Blank-line separation alone is invisible — it is too easy to deviate
from the pattern without explicit comment markers. The labels make the structure
scannable and enforceable at a glance.

## Idiomatic matchers — never dot-access in `expect()`

- `expect(result).toHaveProperty('prop', value)` NOT
  `expect(result.prop).toBe(value)`
- `expect(arr).toHaveLength(3)` NOT `expect(arr.length).toBe(3)`
- `expect(val).toBeNull()` NOT `expect(val).toBe(null)`
- `expect(val).toBeUndefined()` NOT `expect(val).toEqual(undefined)`

## Test names never start with "should"

Use outcome statements as continuations of `it()`.

## Isolation

- Reset hoisted mutable variables in `beforeEach`.
- Mock external dependencies only or at SDK boundaries, never internal modules.

## How to apply

Run through this checklist mentally for every `expect()` and every `it()` block
before considering the test done.
