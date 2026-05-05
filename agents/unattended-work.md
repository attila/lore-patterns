---
tags: [agent, unattended, command, bash, composite, git, gh, pnpm, typescript, worktree, universal]
---

# Unattended Work

## Commands to avoid in "Don't Ask" mode

The matcher checks literal command strings; metacharacters that expand
at runtime break `Bash(<binary>:*)` allowlist matches. Avoid:

- Composite shell: pipes, for-loops, `&&`/`;`, subshells `$(...)`,
  process substitution `<(...)`, redirects `>`/`<`/`2>&1`.
- `cd /path && ...` — use `git -C /path` or built-in `EnterWorktree`
  (switches CWD natively; `Bash(node:*)`, `Bash(pnpm:*)`,
  `Bash(cargo:*)` keep matching).
- `mkdir {a,b}` below 5 paths — flagged for approval; use separate
  calls.
- `$(...)` in arguments, e.g. `aws ... "$(date ...)"` — pre-compute
  from a prior tool result.
- Stdio redirects — for backgrounded output set
  `run_in_background: true` (read via `Read`); for file output use
  tool-native flags (`--output-file`, `--jq`, `--body-file`,
  `git commit -F`).
- Heredocs on `gh pr create`/`pr edit --body` — write to
  `/tmp/pr-body-*.md` and pass `--body-file`; `$(cat <<EOF...)` breaks
  the matcher.
