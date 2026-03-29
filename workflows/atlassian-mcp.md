---
tags: [ atlassian, jira, mcp, best-practice, semantics ]
---

# Atlassian MCP createIssueLink has swapped inward/outward semantics

When creating "Blocks" links via MCP, inwardIssue is the BLOCKER and outwardIssue is the BLOCKED issue — opposite of
intuition.

The `mcp__claude_ai_Atlassian__createIssueLink` tool has **swapped semantics** for `inwardIssue` and `outwardIssue`
compared to what the Jira API documentation suggests.

To create **"A blocks B"** (A is a prerequisite for B):

- `inwardIssue: A` (the blocker / prerequisite)
- `outwardIssue: B` (the blocked / dependent)

**Why:** Discovered on 2026-03-12 when all dependency links from a plan were created backwards. The MCP tool's
parameter naming is counterintuitive — `inwardIssue` maps to the outward (blocker) side in Jira's UI, and `outwardIssue`
maps to the inward (blocked) side.

**How to apply:** Every time you use `createIssueLink` with type "Blocks", remember to swap: the blocker goes in
`inwardIssue`, the blocked goes in `outwardIssue`. Verify by reading one ticket after creating links.
