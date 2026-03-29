---
tags: [ agent, unattended, command, bash, composite, git, gh ]
---

# Unattended Work

Unattended agentic sessions are critical in high throughput environments. Follow these guidelines to ensure that
unattended work is done in a safe and secure manner.

## Commands to avoid

- Avoid composite and clever shell commands (for loops, pipes, subshells, etc.). Use simple individual commands
  instead — run them in parallel when independent.
- Do not use brace expansion (`{a,b}`) in commands when creating fewer than 5 directories. Instead, list paths
  separately so each is individually approved and unattended sessions run efficiently. Brace-expanded mkdir gets flagged
  for manual approval, blocking unattended execution. Make multiple mkdir calls.
- Avoid running composite Bash commands, e.g. (cd /tmp && git clone https://github.com/user/repo). Many CLI tools allow
  working from a different pwd, take advantage of that (e.g. `git -C /path/to/worktree add file.txt`) but this doesn't
  help for moon/pnpm/proto

## GitHub Pull Requests

- Only use the `gh` CLI if available
- When creating PRs with `gh pr create`, use `--body-file /tmp/pr-body.md` instead of `--body` with inline strings or
  heredocs. Write the body to a tmp file first, then reference it. Inline and heredoc approaches get blocked by
  permission settings.
- Use unique tmp filenames to avoid collisions with other sessions.

## Worktrees

- `Bash(cd:*)` does NOT match compound commands like `cd /path && moon run ...` in don't-ask mode. The permission system
  evaluates the full command string and doesn't decompose compound commands.
- **How to apply:** Use `EnterWorktree` (built-in tool) instead of manual `git worktree add` + compound `cd` commands.
  `EnterWorktree` switches the session CWD natively, so all existing `Bash(moon:*)`, `Bash(pnpm:*)`, `Bash(proto:*)`
  permissions match normally. No `cd && ...` needed.
