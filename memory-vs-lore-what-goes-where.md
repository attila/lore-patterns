---
tags: [memory, lore, personal-knowledge, process]
---

# Memory vs lore — what goes where

Memory (`~/.claude/projects/<key>/memory/`) is workspace-specific: this
team's quirks, this monorepo's conventions, ongoing initiatives, this
project's Jira behaviour. Loaded only when working in that project.

Lore (this knowledge base) is portable: durable, cross-project engineering
wisdom that follows me anywhere. Loaded everywhere.

Before keeping or creating a memory, search lore for overlap. If lore
already covers it, the memory is redundant — drop. If the content is
portable but not yet in lore, migrate it (with the right title and tags)
and drop the memory after migration. Don't keep the same pattern in both
places — that creates drift and dilutes lore as the source of truth.

Always report findings before migrating or dropping; don't act
unilaterally on memory cleanup.
