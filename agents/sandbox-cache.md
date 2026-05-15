---
tags: [agent, sandbox, cache, xdg, gh, gcloud, cargo, home-directory, permission]
---

# Sandbox cache redirection

`gh`, `gcloud`, `cargo`, and most XDG-compliant tools write to `~/.cache/<tool>`
by default. In sandboxed shells where `~/.cache` is restricted (read-only,
missing, or a sentinel file), these calls fail with a permission or "file
exists" error before doing any real work.

## Trigger

A CLI tool fails with a `~/.cache` permission or "file exists" error in a
sandboxed environment, for example:

```
failed to get run log: creating cache directory: mkdir /Users/.../.cache: file exists
```

Common with `gh run view --log-failed` and `gh run download <id>`.

## Action

Set `XDG_CACHE_HOME` inline for the failing command, pointing at a writable
location:

```bash
XDG_CACHE_HOME=/tmp/gh-cache gh run view <id> --log-failed
XDG_CACHE_HOME=/tmp/gh-cache gh run download <id>
XDG_CACHE_HOME=/tmp/gh-cache gh api repos/{owner}/{repo}/actions/runs/<id>/jobs
```

Use a per-tool sub-directory (`/tmp/gh-cache`, `/tmp/cargo-cache`) so successive
invocations stay separated and the override does not touch the user's home
directory.

**Why:** XDG base-directory compliance is widespread; the redirect is a
one-token fix that avoids modifying the sandbox or the user's environment.

**How to apply:** Set the variable inline for the single command, not as a shell
export — keeps the override scoped and visible in command history.

## Counter-cases

- Tools that ignore XDG (some macOS tools hard-code `~/Library/Caches/<tool>`):
  the override has no effect. Check the tool's documentation.
- Permission errors unrelated to the cache directory: redirection will not help;
  diagnose the actual error before reaching for `XDG_CACHE_HOME`.
