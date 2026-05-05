---
tags: [pull-request, github, template, dependencies]
---

# PR description templates

Formatting details for PR bodies. Reach for these when writing or updating a
pull request description — they are not needed on every git-adjacent tool call,
so they live outside the always-on `workflows/git-branch-pr.md` rules.

## Before writing a PR description

Search for `.github/pull_request_template.md` and use it as the starting
structure. If no template exists, use the formatting rules below.

## PR body formatting

- Markdown-style checkboxes with `[ ]` for unchecked and `[x]` for checked for
  TODOs and dependencies.
- Links to other PRs use the bare URL in lists.
- Links to commits use the bare URL.
- When linking to code, always use SHA-pinned blob URLs.

## Marking dependencies in PR descriptions

- Use a "Dependencies" section with a checklist of bare GitHub PR URLs.
  Checked = resolved, unchecked = blocking. GitHub auto-renders bare PR URLs
  with title and status; manual descriptions duplicate that and go stale when
  titles change.
  - how to apply
    ```markdown
    ## Dependencies

    - [x] https://github.com/{user}/{repo}/pull/31253
    - [ ] https://github.com/{user}/{other-repo}/pull/99
    ```
  - Rules:
    - Section title: `## Dependencies` (not "Infrastructure dependency" or
      similar).
    - Full PR URLs only — no shortened `#N` refs, no manual title, description
      or link text. Cross-repo links need full URLs anyway.
    - `[x]` for merged/resolved, `[ ]` for pending/blocking.
    - No strikethrough, no verbose resolved/blocked prose.
    - Reference tickets (Jira, Linear, etc.) live as markdown reference links
      at the bottom of the PR body: `[XYZ-234]: https://...`.

