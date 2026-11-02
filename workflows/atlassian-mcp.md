---
tags: [ atlassian, jira, confluence, mcp, issue-link, semantics, markdown, adf, table ]
---

# Atlassian MCP quirks

## createIssueLink has swapped inward/outward semantics

For directional Jira link types, `inwardIssue` is the SUBJECT of the outward
verb and `outwardIssue` is the OBJECT — opposite of intuition.

The `mcp__claude_ai_Atlassian__createIssueLink` tool's `inwardIssue` /
`outwardIssue` naming reads as grammatical direction but actually encodes the
sentence's subject/object:

> `inwardIssue` [outward verb] `outwardIssue`

**Empirically confirmed** for these link types:

| Link type        | outward verb | inwardIssue is…                                  | outwardIssue is…                             |
| ---------------- | ------------ | ------------------------------------------------ | -------------------------------------------- |
| Blocks           | blocks       | the blocker / prerequisite                       | the blocked / dependent                      |
| Duplicate        | duplicates   | the redundant / lesser ticket                    | the canonical / kept ticket                  |
| Problem/Incident | causes       | the causer (i.e. the regression-introducing one) | the "caused-by" (i.e. the regression ticket) |

**Why:** Originally discovered 2026-03-12 with "Blocks" links created backwards.
Re-confirmed 2026-05-19 with a "Duplicate" link created backwards on SLP-2404 →
SLP-2426 — same shape, same trap.

**How to apply:** Before every `createIssueLink` call, write the sentence in
English using the outward verb, then assign the subject to `inwardIssue` and the
object to `outwardIssue`. Verify by reading one of the two tickets after
creating the link. **If using a link type not in the table above** (Clones,
Problem/Incident, Issue split, Relates, etc.), test the orientation with a
throwaway link first — the pattern is likely but unverified.

## Markdown writes drop tables nested in list items

Atlassian MCP writes with markdown content (Jira issue edits, Confluence page
create and update) round-trip through Atlassian Document Format. A markdown
table nested inside a list item is silently dropped on the way, sometimes with
the whole enclosing bullet, and the call still reports success.

**How to apply:** keep tables out of list items, either as flat `key: value`
bullets or as a table in its own paragraph. Read back the body echoed in the
response to confirm nothing was lost.
