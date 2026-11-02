---
tags: [github, gh, api, graphql, rest, mutations, bulk, batch, resolve, review-thread, pull-request, review, pending, raw-field, resource-limits]
---

# GitHub API through gh

## GraphQL bulk mutations: cap each document at ≤10

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

## `gh api -f` sends `@path` literally

`-f`/`--raw-field` sends its value verbatim, so `-f body=@reply.md` posts the
string `@reply.md`, not the file. Curl's `--data @file` habit misleads here.
`-F`/`--field` does read `@<path>` (per `gh api --help`).

**How to apply:** short bodies go inline with `-f`. For long ones, use
`-F body=@reply.md`, or build the payload and pass it with `--input`:

```bash
jq -n --rawfile body reply.md '{body: $body}' > payload.json
gh api repos/{owner}/{repo}/issues/{n}/comments --input payload.json
```

Before posting many comments, post one and read it back.

## Pending versus submitted pull request reviews

A pull request review created without an `event` field stays `PENDING`: a
private draft the author can still edit or delete. Creating it with an `event`
(`COMMENT`, `APPROVE`, `REQUEST_CHANGES`) submits it in the same call. A pending
review is submitted later through
`POST /repos/{owner}/{repo}/pulls/{pull_number}/reviews/{review_id}/events`.
Submitting publishes the review at once, and a submitted review cannot be
deleted.

**How to apply:** map "stage", "draft" and "leave pending" to a review created
without `event`. Treat "submit" and "publish" as the irreversible step and name
that before taking it.
