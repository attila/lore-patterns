---
tags: [github, graphql, api, mutations, bulk, batch, resolve, review-thread, pull-request, resource-limits]
---

# GitHub GraphQL bulk mutations

## Cap each document at ≤10 mutations

Split bulk operations into batches of ≤10 mutations per
`gh api graphql -F query=@chunk.graphql` call. GitHub's GraphQL API enforces an
undocumented per-document resource limit (empirical cutoff ~12); documents above
the ceiling return `RESOURCE_LIMITS_EXCEEDED` for trailing operations while
earlier ones commit permanently. Discovered 2026-05-05 when a 16-mutation
PR-thread-resolution document had its last four operations rejected after the
first 12 wrote successfully.

On partial failure, parse `errors[].path` and rebuild a follow-up document
containing only the failed aliases. Never retry the original document — the
committed 12 will produce duplicate replies and "already resolved" errors. The
signature is distinctive: partial `data` with field-level `null`s for rejected
aliases plus an `errors[]` array of `type: "RESOURCE_LIMITS_EXCEEDED"`.
