---
tags: [pull-request, github, template, dependencies]
---

# PR description templates

Formatting details for PR bodies. Reach for these when writing or updating a
pull request description — they are not needed on every git-adjacent tool call,
so they live outside the always-on `workflows/git-branch-pr.md` rules.

## PR body formatting

- PR descriptions always respect existing pull request templates.
- Markdown-style checkboxes with `[ ]` for unchecked and `[x]` for checked for
  TODOs and dependencies.
- Links to other PRs use the bare URL in lists.
- Links to commits use the bare URL.
- When linking to code, always use SHA-pinned blob URLs.

## Marking dependencies in PR descriptions

- PR dependencies use a simple "Dependencies" section with a checklist of full
  GitHub PR URLs and a brief dash-separated description. Checked = resolved,
  unchecked = blocking.
  - how to apply
    ```markdown
    ## Dependencies

    - [x] https://github.com/{user}/{repo}/pull/31253
    - [ ] https://github.com/{user}/{other-repo}/pull/99
    ```
  - Rules:
    - Section title: `## Dependencies` (not "Infrastructure dependency" or
      similar)
    - Full PR URLs, not shortened `#N` refs (cross-repo links need full URLs
      anyway)
    - Optional brief description after dash, lowercase
    - `[x]` for merged/resolved, `[ ]` for pending/blocking
    - No strikethrough, no verbose resolved/blocked prose
    - Reference ticket (Jira, Linear, etc) can be a markdown reference link at
      the bottom of the PR body: `[XYZ-234]: https://...`
