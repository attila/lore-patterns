---
tags: [ testing, conventions, javascript, typescript, vitest, jest ]
---

# Test writing conventions checklist

Before writing or modifying any test file, mentally run this checklist on every `it()` block.

1. AAA structure with blank-line separation AND comment labels
    - Each phase on its own line(s) MUST be labelled: `// Arrange`, `// Act`, `// Assert`, or `// Act & Assert`
    - Arrange (optional) → optional blank line → Act → optional blank line → Assert
        - Add blank lines only to longer blocks to help readability.
    - Act+Assert may merge for trivial one-liners (no comment needed)
    - BAD: blank-line separation without comment labels — the labels must be visible
2. Idiomatic matchers — never dot-access in `expect()`. Examples:
    - `expect(result).toHaveProperty('prop', value)` NOT `expect(result.prop).toBe(value)`
    - `expect(arr).toHaveLength(3)` NOT `expect(arr.length).toBe(3)`
    - `expect(val).toBeNull()` NOT `expect(val).toBe(null)`
    - `expect(val).toBeUndefined()` NOT `expect(val).toEqual(undefined)`
3. Test namesn ever start with "should" — use outcome statements as continuations of `it()`
4. Isolation
    - Reset hoisted mutable variables in `beforeEach`
    - Mock external deps only or at SDK boundaries, never internal modules
    - **Why:** Blank-line separation alone is invisible — it's too easy to deviate from the pattern without explicit
      comment markers. The labels make the structure scannable and enforceable at a glance.
    - **How to apply:** Run through this checklist mentally for every `expect()` and every `it()` block before
      considering the test done. When in doubt, re-read `.ai/rules/project-testing.md`.
