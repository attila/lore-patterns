---
language: rust
tags: [conventions, rust, licensing, edition, msrv, release, binary-size]
---

# Rust Project Conventions

## Edition and MSRV

Set `edition = "2024"` in Cargo.toml. Pin the minimum supported Rust version via
`rust-version = "1.85"` in Cargo.toml and in `rust-toolchain.toml`. Never adopt
a new edition automatically — edition updates are intentional and tested.

## Licensing

Use dual MIT/Apache-2.0 licensing (Rust ecosystem convention). Include both
`LICENSE-MIT` and `LICENSE-APACHE` files in the repository root. Set
`license = "MIT OR Apache-2.0"` in Cargo.toml.

## Release profile

Optimise release builds for small binary sizes:

```toml
[profile.release]
strip = true
lto = true
opt-level = "z"
```

Always set all three options together. `strip` removes debug symbols, `lto`
enables link-time optimisation across crates, and `opt-level = "z"` prioritises
size over speed.
