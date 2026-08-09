# Agent instructions

Working rules for changing patterns in this repository.

## Delivery

Trunk-based. Signed commits straight to `trunk`, then push.

## Before every push

A pattern is judged by how it retrieves, not by how it reads.

1. **Search for overlap before writing.** If a pattern already covers the
   ground, edit that one. Two patterns on one subject compete for the same
   queries and dilute both.
2. **Prove the retrieval.** Run `lore ingest --file <path>`, which indexes an
   uncommitted file and leaves delta state alone. Then run one plain-language
   query that must return the changed chunk, and two or three neighbouring
   queries that must not. A pattern answering only to its own keywords has a
   recall problem. One answering to everything has a bleed problem. Both are
   worth catching before the pattern is live.
3. Format changes with `dprint fmt`. Run `bin/install-hooks` once per checkout
   and the pre-commit hook enforces it for you.

## Review

Show the change and the retrieval results, then wait for a go before pushing.
Every change, including a one-line edit.
