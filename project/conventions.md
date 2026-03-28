---
tags: [conventions, git, licensing, rust]
---

# Project Conventions

## Git workflow

- Feature branches only — never push directly to main.
- Squash merges to main. Require branch to be up-to-date before merging.
- Conventional commit messages: `feat:`, `fix:`, `chore:`, `doc:`.
- Commits are GPG-signed automatically via git config.
- Pre-commit hook runs `dprint check` to catch formatting issues before push.

## Licensing

Dual MIT/Apache-2.0 (Rust ecosystem convention). Both `LICENSE-MIT` and `LICENSE-APACHE` files in the
repo root. `license = "MIT OR Apache-2.0"` in Cargo.toml.

## Edition and MSRV

- Rust 2024 edition (`edition = "2024"` in Cargo.toml).
- MSRV pinned via `rust-version = "1.85"` in Cargo.toml and `rust-toolchain.toml`.
- Edition updates are intentional and tested — not adopted automatically.

## Release profile

Optimized for small binary size:

```toml
[profile.release]
strip = true
lto = true
opt-level = "z"
```

## SQLite conventions

- Bundled SQLite via rusqlite `bundled` feature — no system library dependency.
- WAL mode + SYNCHRONOUS=NORMAL for performance.
- `busy_timeout=5000` for concurrent access tolerance.
- sqlite-vec loaded via `sqlite3_auto_extension` (process-global, called once via `std::sync::Once`).
- Database is a derived artifact — safe to delete and rebuild from source markdown via `lore ingest`.

## Trait boundaries for testability

Use traits at external service boundaries (network, third-party APIs). Keep internal boundaries
concrete. The rule: if it requires a running service or network access, abstract it behind a trait.
If it's bundled and fast (SQLite, filesystem), use the real thing in tests.
