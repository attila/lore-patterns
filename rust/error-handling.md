---
tags: [rust, error-handling, anyhow, result]
---

# Error Handling

## Application errors with anyhow

Use `anyhow::Result` for all fallible functions in application code. It provides
context chaining, backtraces, and ergonomic error propagation without defining
custom error types.

```rust
use anyhow::Result;

fn load_config(path: &Path) -> Result<Config> {
    let contents = std::fs::read_to_string(path)
        .map_err(|_| anyhow::anyhow!("Config not found at {}. Run 'lore init' first.", path.display()))?;
    let config: Config = toml::from_str(&contents)?;
    Ok(config)
}
```

## Error propagation rules

- Library code returns `anyhow::Result`. No panics in library functions.
- `main()` catches all errors, prints them with `eprintln!("Error: {e}")`, and
  exits with code 1.
- Use `anyhow::bail!` for early returns with error messages.
- Use `.map_err()` to add context to low-level errors (e.g., file not found →
  "Config not found").

## When to collect errors vs. propagate

For batch operations where partial success is acceptable (e.g., ingesting many
files), collect errors into a `Vec<String>` and continue processing. Report the
error count at the end. For single-item operations (e.g., adding one pattern),
propagate immediately with `?`.

## No panics in library code

- Never use `.unwrap()` or `.expect()` in library code. Use `?` instead.
- Exception: `.unwrap()` is acceptable in tests and in cases where the invariant
  is proven (e.g., regex compilation of a constant pattern).
- Use `f64::total_cmp()` instead of `partial_cmp().unwrap()` for float sorting.
