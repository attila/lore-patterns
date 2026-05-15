---
tags: [engineering, code-review, package-design, consistency]
---

# Mirror references exactly

When the task is to mirror an existing reference (sibling package, parallel
implementation, established pattern), match every style decision from the
reference precisely:

- Variable types and shapes (e.g. `let instance: T` without `| undefined` if the
  reference uses that)
- JSDoc / docstring format (preserve `@param {Type} name` style if used)
- Inline comments (don't add cross-references to other packages — each package
  stands on its own)

Before accepting reviewer-agent suggestions that touch style, check whether the
change makes the new code diverge from its reference. If yes, skip the
suggestion. The priority is structural fidelity, not theoretical type-safety
improvements.

The reference is the contract; "improvements" are debt added against it.
