---
tags: [rust, testing, mocking, sqlite, fakes]
---

# Testing Strategy

## Core principle: real dependencies, fake externals

Use real implementations for fast, bundled dependencies. Use hand-written fakes
for external network services. The boundary is: if it requires a running service
or network access, fake it.

| Dependency  | Strategy                                   | Reason                                            |
| ----------- | ------------------------------------------ | ------------------------------------------------- |
| SQLite      | Real (in-memory via `:memory:`)            | Bundled, fast, no setup                           |
| sqlite-vec  | Real (loaded via `sqlite3_auto_extension`) | Compiled into binary                              |
| Ollama      | Fake (`FakeEmbedder`)                      | External HTTP service, slow, not always available |
| Git         | Real (tempdir + `git init`)                | Available everywhere, fast                        |
| File system | Real (tempdir)                             | Fast, isolated via `tempfile::tempdir()`          |

## FakeEmbedder pattern

Define test doubles as `pub(crate)` at module scope behind `#[cfg(test)]`,
outside the `mod tests` block. This makes them importable by other modules' test
code without affecting production builds.

```rust
#[cfg(test)]
pub(crate) struct FakeEmbedder { dimensions: usize }

impl Embedder for FakeEmbedder {
    fn embed(&self, input: &str) -> Result<Vec<f32>> { /* deterministic hash-based vector */ }
    fn dimensions(&self) -> usize { self.dimensions }
}
```

Use 768 dimensions (matching `nomic-embed-text`) by default. The cost is
negligible for in-memory SQLite and avoids divergence from production vector
separation behavior.

## Test organization

- **Unit tests:** Inline `#[cfg(test)] mod tests` in each source file.
- **Integration tests:** `tests/` directory using `assert_cmd` + `predicates`
  for CLI testing.
- **Snapshot tests:** `insta::assert_json_snapshot!` for JSON-RPC responses
  (requires `insta = { version = "1", features = ["json"] }`).
- **Test fixtures:** Committed markdown files in `tests/fixtures/` for ingestion
  tests.

## Prefer hand-written fakes over mockall

Hand-written fakes are simpler, more readable, and give full control over
behavior. Reserve mockall for cases where the interface has many methods and you
only need to stub one.
