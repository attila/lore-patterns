---
tags: [conventions, git, licensing, rust, compiling]
---

# Project Conventions

## Git workflow

- Pre-commit hook runs `dprint check` to catch formatting issues before push.

## Licensing

Dual MIT/Apache-2.0 (Rust ecosystem convention). Both `LICENSE-MIT` and `LICENSE-APACHE` files in the
repo root. `license = "MIT OR Apache-2.0"` in Cargo.toml.

## Edition and MSRV

- Rust 2024 edition (`edition = "2024"` in Cargo.toml).
- MSRV pinned via `rust-version = "1.85"` in Cargo.toml and `rust-toolchain.toml`.
- Edition updates are intentional and tested — not adopted automatically.

## Release profile

Optimise for small binary sizes:

```toml
[profile.release]
strip = true
lto = true
opt-level = "z"
```
