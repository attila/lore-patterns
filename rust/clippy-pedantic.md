---
tags: [rust, clippy, linting, code-quality]
---

# Clippy Pedantic Configuration

## Approach: warn locally, deny in CI

Enable pedantic at warn level in `Cargo.toml` with priority -1, then selectively allow noisy lints.
CI runs `cargo clippy --all-targets -- -D warnings` so pedantic lints block merges but don't break
local iteration.

```toml
[lints.clippy]
pedantic = { level = "warn", priority = -1 }
missing_errors_doc = "allow"
missing_panics_doc = "allow"
module_name_repetitions = "allow"
must_use_candidate = "allow"
```

## Common pedantic fixes

- `&PathBuf` parameters → `&Path` (clippy::needless_pass_by_value)
- `map().unwrap_or()` → `map_or()` (clippy::option_if_let_else)
- `map_or(false, ...)` → `is_some_and(...)` (idiomatic Rust 2024)
- Manual char comparison → char array: `[' ', '-']` instead of closure
- `partial_cmp().unwrap()` → `total_cmp()` for float sorting

## Unsafe code policy

`unsafe_code = "deny"` globally in `[lints.rust]`. When FFI requires unsafe (e.g., sqlite-vec
registration via `sqlite3_auto_extension`), use a targeted `#[allow(unsafe_code)]` on the smallest
possible function with a `// SAFETY:` comment explaining the invariant.

```rust
#[allow(unsafe_code)]
fn register_sqlite_vec() {
    // SAFETY: sqlite3_vec_init is the documented entrypoint for the sqlite-vec extension.
    // transmute converts the function pointer to the type expected by sqlite3_auto_extension.
    unsafe { ... }
}
```
