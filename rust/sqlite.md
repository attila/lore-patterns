---
tags: [ conventions, rust, sqlite ]
---

# SQLite conventions

- Bundled SQLite via rusqlite `bundled` feature — no system library dependency.
- WAL mode + SYNCHRONOUS=NORMAL for performance.
- `busy_timeout=5000` for concurrent access tolerance.
- sqlite-vec loaded via `sqlite3_auto_extension` (process-global, called once
  via `std::sync::Once`).
- Database is a derived artifact — safe to delete and rebuild from source
  markdown via `lore ingest`.

## Trait boundaries for testability

Use traits at external service boundaries (network, third-party APIs). Keep
internal boundaries concrete. The rule: if it requires a running service or
network access, abstract it behind a trait. If it's bundled and fast (SQLite,
filesystem), use the real thing in tests.
