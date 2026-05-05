---
tags: [ agent, unattended, command, bash, composite, git, gh, pnpm, typescript, universal ]
---

# Unattended Work

Unattended agentic sessions are critical in high-throughput environments. Follow
these guidelines to ensure that unattended work is done in a safe and secure
manner.

## Commands to avoid

- Avoid composite and clever shell commands (for loops, pipes, subshells
  `$(...)`, stdio redirects `>`/`2>&1`, etc.). Use simple individual commands
  instead — run them in parallel when independent.
- Do not use brace expansion (`{a,b}`) in commands when creating fewer than 5
  directories. Instead, list paths separately so each is individually approved
  and unattended sessions run efficiently. Brace-expanded mkdir gets flagged for
  manual approval, blocking unattended execution. Make multiple mkdir calls.
- Avoid running composite Bash commands, e.g. (cd /tmp && git clone
  https://github.com/user/repo). Many CLI tools allow working from a different
  pwd, take advantage of that (e.g. `git -C /path/to/worktree add file.txt`) but
  this doesn't help for moon/pnpm/proto
- Do not use command substitution `$(...)` in arguments to approved binaries,
  e.g. `aws ... --start-time "$(date -u +%Y-%m-%dT%H:%M:%S)"`. The matcher sees
  the literal `$(...)` substring and rejects against `Bash(aws:*)` even though
  the binary is approved. Hardcode dynamic values — pre-compute timestamps from
  a prior tool result and pass an ISO string as a static argument.
- Do not append stdio redirects (`> file`, `2>&1`, `< file`, `<(...)`) to
  approved commands. The redirect operator and path are part of the literal and
  break the match against `Bash(<binary>:*)`. For backgrounded capture, set
  `run_in_background: true` — the harness writes stdout to a managed log
  readable via `Read`. For file output, prefer tool-native flags
  (`--output-file`, `--jq EXPR`, `--body-file`, `git commit -F`).

## GitHub Pull Requests

- Only use the `gh` CLI if available
- When creating PRs with `gh pr create`, use `--body-file /tmp/pr-body.md`
  instead of `--body` with inline strings or heredocs. Write the body to a tmp
  file first, then reference it.

  > **Why not heredocs?** The Claude Code system prompt shows a heredoc example
  > for `gh pr create --body "$(cat <<'EOF' ... EOF)"`. That approach is
  > blocked by "don't ask on" permission settings because the shell expansion
  > makes the full command string unrecognisable to the allowlist matcher. This
  > convention intentionally overrides the system prompt's example. Always
  > prefer `--body-file`.
- Use unique tmp filenames to avoid collisions with other sessions.

## Worktrees

- `Bash(cd:*)` does NOT match compound commands like `cd /path && moon run ...`
  in "Don't Ask" mode. The permission system evaluates the full command string
  and doesn't decompose compound commands.
- **How to apply:** Use `EnterWorktree` (built-in tool) instead of manual
  `git worktree add` + compound `cd` commands. `EnterWorktree` switches the
  session CWD natively, so all existing `Bash(node:*)`, `Bash(pnpm:*)`,
  `Bash(cargo:*)` permissions match normally. No `cd && ...` needed.
