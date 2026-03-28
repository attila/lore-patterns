---
tags: [ci, github-actions, rust, toolchain]
---

# GitHub Actions CI for Rust

## Toolchain setup: use setup-rust-toolchain, not dtolnay/rust-toolchain

`dtolnay/rust-toolchain` does NOT read `rust-toolchain.toml` automatically. It requires an explicit
`toolchain` input, creating drift between CI and the project's toolchain spec.

Use `actions-rust-lang/setup-rust-toolchain@v1` instead — it reads `rust-toolchain.toml` natively and
includes built-in cargo caching (no separate `Swatinem/rust-cache` step needed).

```yaml
- uses: actions-rust-lang/setup-rust-toolchain@v1
  with:
    rustflags: ""
```

The `rustflags: ""` is critical: without it, the action sets `RUSTFLAGS="-D warnings"` by default,
which conflicts with passing `-- -D warnings` directly to clippy.

## Gateway job pattern

Use a single `ci` gateway job that depends on all gate jobs. Branch protection points at this one
check, so adding or removing gate jobs doesn't require updating branch protection rules.

The gateway must use **allowlist logic** with `if: always()`:

```yaml
ci:
  needs: [fmt, clippy, test, deny, doc]
  if: always()
  steps:
    - run: |
        results=("${{ needs.fmt.result }}" "${{ needs.clippy.result }}" ...)
        for r in "${results[@]}"; do
          if [[ "$r" != "success" ]]; then
            echo "Job failed with result: $r"
            exit 1
          fi
        done
```

**Why allowlist, not denylist:** GitHub Actions job results include `skipped`, which is neither
`failure` nor `cancelled`. A denylist approach (`!= failure && != cancelled`) silently passes on
skipped jobs. The allowlist (`!= success` means fail) treats any non-success state as a failure.

**Why `if: always()`:** Without it, the gateway is skipped when dependencies are skipped, which
GitHub treats as a passing check.

## Tool installation

Use `taiki-e/install-action@v2` for installing Rust tools (`just`, `dprint`, `cargo-deny`). It
caches binaries and handles cross-platform installation.
