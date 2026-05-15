---
tags: [rustup, install, toolchain, troubleshooting]
---

# Rustup: repairing an interrupted toolchain install

## Symptom

`~/.rustup/toolchains/<channel>-<host>/` exists but is missing key binaries —
usually `cargo` and `rustc` while `clippy`/`rustfmt` are present. Invoking a
present binary fails with a missing-library error — on macOS:
`dyld: Library not loaded: librustc_driver-*.dylib`; on Linux:
`error while loading shared libraries: librustc_driver-*.so`. The library ships
in the un-extracted `rust` component.

Diagnostic tell: `rustup toolchain install <channel>` short-circuits with
`debug: toolchain is already up to date` while the next line admits
`(error reading rustc version)`.

## Root cause

Rustup writes `~/.rustup/update-hashes/<channel>-<host>` **before** extracting
components. If the install is interrupted (Ctrl-C, network drop, sleep,
container kill), the hash file persists and shorts every subsequent `install` —
there is no filesystem integrity check.

## Fix

`install` alone cannot repair this. Uninstall (which clears the hash), then
reinstall:

    rustup toolchain uninstall <channel>-<host>
    rustup toolchain install <channel>

Component archives in `~/.rustup/downloads/` are reused on hash-match, so
redownload cost is usually zero.

## Don't be fooled by `rustup show`

It lists the toolchain as installed based on directory existence, not
completeness. Same with `rustup component add` — fine for backfilling peripheral
components, useless when `cargo`/`rustc` are what's missing.
