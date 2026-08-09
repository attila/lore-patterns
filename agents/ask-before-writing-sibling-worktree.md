---
tags: [git, worktree, collaboration, safety]
---

# Ask before writing to a sibling worktree

A sibling worktree can carry uncommitted work invisible from the one you're in —
a different branch checked out, files staged, edits mid-flight. Treat it as
someone else's workspace: read freely, but don't run `git checkout`,
`git reset`, a formatter, or any command that mutates its tracked files without
asking first.

Applies even when the worktree looks idle. Uncommitted changes don't show up in
`git log`, and a stale read is indistinguishable from an empty one.
