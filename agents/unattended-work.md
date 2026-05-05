---
tags: [ agent, unattended, command, bash, composite, git, gh, pnpm, typescript, worktree, universal ]
---

# Unattended Work

## Allow list

- Use spaces, not colons: `Bash(gh pr edit *)`.
- Edits load on session start; restart to apply.

## Commands to avoid in "Don't Ask" mode

Matcher reads literal strings; runtime-expanded metacharacters break
`Bash(<binary>:*)` matches. Avoid:

- Composite shell: pipes, `&&`/`;`, `$(...)`/`<(...)`, redirects.
- `cd /path && ...` — use `git -C` or `EnterWorktree` (switches CWD;
  `Bash(<binary>:*)` patterns still match).
- `mkdir {a,b}` below 5 paths — flagged; use separate calls.
- `$(...)` in arguments — pre-compute from a prior tool result.
- Stdio redirects — `run_in_background: true` for streams; tool flags for
  files (`--output-file`, `--jq`, `--body-file`, `git commit -F`).
- Heredocs on `gh pr create/edit --body` — write to a temp file, pass
  `--body-file`.
