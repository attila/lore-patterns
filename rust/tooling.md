---
tags: [ tooling, rust, dprint, just, cargo-deny, formatting ]
---

# Project Tooling

## Task runner: just

`just` is the task runner for all local and CI operations. Every quality gate has a recipe. `just ci`
runs them all in sequence — if local CI passes, remote CI passes.

Key recipes: `fmt`, `clippy`, `test`, `deny`, `doc`, `ci`, `setup` (git hooks).

## Formatting: dprint

dprint is the single formatter for all file types: Rust (via exec/rustfmt), Markdown, TOML, JSON.
This replaces standalone `rustfmt.toml` — rustfmt config lives inside dprint's exec plugin settings.

Key config:
- Line width 100
- Markdown: `textWrap: "always"` (hard-wraps prose at 100 chars)
- Rust: delegates to `rustfmt --edition 2024`
- Pre-commit hook (`.githooks/pre-commit`) runs `dprint check`

## Dependency auditing: cargo-deny

`deny.toml` checks advisories, licenses, bans, and sources. License allowlist includes standard
permissive licenses (MIT, Apache-2.0, BSD-3-Clause, ISC, Unicode-3.0, Zlib) plus
CDLA-Permissive-2.0 (needed by `webpki-roots` via ureq).

Note: cargo-deny may require a newer Rust than the project's MSRV. Install with
`cargo +stable install cargo-deny` if the project pins an older toolchain.

## Toolchain pinning

`rust-toolchain.toml` is the single source of truth for the Rust version, used by both local
development and CI. Pin the channel to a specific version (e.g., `"1.85"`) with `clippy` and
`rustfmt` components. Toolchain updates are intentional, not automatic.
