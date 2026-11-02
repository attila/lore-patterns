---
tags: [agent, verification, remote-branch, subagent, external-write, retry]
---

# Verify at the source

A claim is only as current as the place it was checked. Three places where the
check usually happens somewhere stale.

## Another repository: read the remote default branch

A fetch does not move the local branch, and a sibling worktree sits at its own
branch point rather than at current main. Before claiming another repository
does or lacks something, fetch, then read the files at the remote branch
(`git show origin/main:<path>`), or first check how far behind the checkout is
(`git log HEAD..origin/main --oneline`). Take extra care when the claim
contradicts what the person you work for said about their own system.

## Subagent identifiers are drafts

Subagent reports pair real identifiers (tickets, pages, pull requests, files,
functions) with the wrong title, status or content, especially after summarising
large result sets. Before quoting a cited identifier to the user or into an
artefact, fetch it from its source. Label anything unchecked as "reported by a
subagent, not verified".

## Read back after a rejected or interrupted write

A write to an external system (tracker, chat, code host) that comes back
rejected or interrupted may still have landed; the client-side rejection does
not prove the server dropped it. Read the target back before retrying, and retry
only if the write is absent; otherwise the retry posts a duplicate.
