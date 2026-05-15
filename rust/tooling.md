---
language: rust
tags: [tooling, rust, dprint, just, cargo-deny, formatting, quality-gate, continuous-integration]
---

# Project Tooling

## Task runner: just

`just` is the task runner for all local and CI operations. Every quality gate
has a recipe. `just ci` runs them all in sequence — if local CI passes, remote
CI passes.

Key recipes: `fmt`, `clippy`, `test`, `deny`, `doc`, `ci`, `setup` (git hooks).

## Quality gate workflow

Always run `just ci` before committing. Never substitute individual commands
such as `cargo test` or `cargo clippy` — they skip formatting checks and other
quality gates that `just ci` runs in sequence. If local CI passes, remote CI
passes.

**Why:** During an early session, an agent ran only `cargo test` and
`cargo clippy`, missing `dprint check` formatting failures in both Markdown and
Rust files. CI failed after push. The full pipeline catches what individual
commands miss.

**How to apply:** After finishing code changes and before any `git add` or
`git commit`, run `just ci`. Even when changes seem trivial or test-only, run
the full pipeline. If justfile recipes were modified, that is even more reason
to verify the recipes themselves work.

## Formatting: dprint

dprint is the single formatter for all file types: Rust (via exec/rustfmt),
Markdown, TOML, JSON. This replaces standalone `rustfmt.toml` — rustfmt config
lives inside dprint's exec plugin settings.

Key config:

- Line width 100
- Markdown: `textWrap: "always"` (hard-wraps prose at 100 chars)
- Rust: delegates to `rustfmt --edition 2024`
- Pre-commit hook (`.githooks/pre-commit`) runs `dprint check`

## dprint version pinning

Never rely on an unpinned formatter version in CI or on a new machine. CI pins
dprint to a specific version (currently 0.53.1) to match local installs. If a
format check failed, the pre-commit hook reports inconsistent formatting, or CI
rejects code that passes locally, suspect a version mismatch before debugging
the formatting itself.

**Why:** Unpinned dprint in CI pulled a newer version with different markdown
formatting defaults, causing CI failures on code that passed locally. Version
drift between local and CI environments produces false negatives — code appears
correctly formatted locally but breaks on the remote check.

**How to apply:** When formatting fails in CI but passes locally, compare
`dprint --version` locally against the version pinned in the CI workflow. If
they differ, that is the cause. When setting up a new machine, run `just setup`
to activate the pre-commit hook, which runs `dprint check` before every commit.

## Dependency auditing: cargo-deny

`deny.toml` checks advisories, licenses, bans, and sources. License allowlist
includes standard permissive licenses (MIT, Apache-2.0, BSD-3-Clause, ISC,
Unicode-3.0, Zlib) plus CDLA-Permissive-2.0 (needed by `webpki-roots` via ureq).

Note: cargo-deny may require a newer Rust than the project's MSRV. Install with
`cargo +stable install cargo-deny` if the project pins an older toolchain.

## Toolchain pinning

`rust-toolchain.toml` is the single source of truth for the Rust version, used
by both local development and CI. Pin the channel to a specific version (e.g.,
`"1.85"`) with `clippy` and `rustfmt` components. Toolchain updates are
intentional, not automatic.
