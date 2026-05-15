---
tags: [git, pull-request, agent-discipline, branch]
---

# Respect explicit branch instructions — don't modify existing PRs uninvited

When instructed to create a "fresh branch", "new worktree", or "new PR", do
exactly that — even if you notice a related branch or open PR with similar work.
Mention the existing branch in passing if relevant, but don't touch it.

Never push commits to an existing PR or branch without explicit permission,
especially one that already has reviewer comments or approvals. Modifying
reviewed code without asking is presumptuous and disruptive: it forces external
reviewers to re-engage with code they already signed off on.

**How to apply:** When the user says "fresh branch off main", "new worktree", or
just "open a new PR", follow that instruction literally. If the user has been
working in a related context, surface the link ("there's an open PR #123 on the
same area") but stop there. Ask before modifying any branch with an open PR.
