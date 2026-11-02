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

## Follow-on documentation for an open PR

When documentation-only follow-on work belongs to the same ticket as an open PR,
don't decide alone between adding to it and opening a new one. Ask once ("on the
open PR or a separate one?") and propose one by the PR's state. For a draft or
unreviewed PR, propose adding to it: additive commits rewrite no history. For an
approved PR, propose a separate one, because a new commit can dismiss existing
approvals and force a fresh review. A separate PR also fits when the
documentation is unrelated to the PR's ticket or reviewers have drawn a tight
scope line.
