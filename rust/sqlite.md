---
language: rust
tags: [conventions, rust, sqlite, rusqlite, wal, sqlite-vec]
---

# SQLite Conventions

## Use bundled SQLite via rusqlite

Always enable the `bundled` feature on rusqlite. Never depend on a system SQLite
library — bundling eliminates version drift between development machines and CI.

## Configure connections for performance

Set these pragmas on every new connection:

- `journal_mode = WAL` — enables concurrent reads during writes.
- `SYNCHRONOUS = NORMAL` — safe with WAL, avoids fsync on every commit.
- `busy_timeout = 5000` — waits up to five seconds on lock contention instead of
  failing immediately.

## Register sqlite-vec once at process start

Load sqlite-vec via `sqlite3_auto_extension`, guarded by `std::sync::Once`. This
registers the extension process-globally so every subsequent connection inherits
it without per-connection setup.

## Treat the database as a derived artefact

The database is rebuilt from source markdown via `lore ingest`. Never store
authoritative data in it — it is safe to delete and regenerate at any time. This
means migration tooling is unnecessary; schema changes are applied by
re-ingesting.

## Trait boundaries for testability

Use traits at external service boundaries (network, third-party APIs). Keep
internal boundaries concrete. If a dependency requires a running service or
network access, abstract it behind a trait. If it is bundled and fast (SQLite,
filesystem), use the real implementation in tests.
