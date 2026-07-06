---
tags: [nono, sandbox, gpg-agent, ssh-agent, commit-signing, sleep-wake, ipc, restart]
applies_when:
  tools: [Bash]
---

# nono: agent-socket rot after host sleep/wake

Inside the nono sandbox, operations that reach a host agent over a unix socket —
`gpg-agent` (commit signing) or `ssh-agent` (SSH, git push) — can fail with IPC
connect call failed / No agent running, while the identical command works in an
unsandboxed shell. Toggling a per-command sandbox switch does not help.

## Cause

nono binds the agent socket at sandbox-process start. If the host sleeps and
wakes while the sandboxed session stays alive, that link is severed and does not
recover in-process. Environmental, not a repo or config fault — the
signing/agent config is unchanged (e.g. commit.gpgsign in ~/.gitconfig is fine).

## Fix

Restart the nono sandbox process and resume the session, then retry — a fresh
process re-establishes the socket. Do not work around it: no --no-gpg-sign, no
"commit/push outside the sandbox", no disabling the sandbox. Those defeat the
isolation nono exists for and usually aren't even needed.

## Trigger

A command inside a nono sandbox fails with gpg-agent/ssh-agent IPC connect call
failed, No agent running, or Could not open a connection to your authentication
agent — especially after the machine has slept — yet the same command works
unsandboxed. Tool-agnostic (Claude Code, Codex, Crush): a nono↔OS interaction,
not an agent-tool bug.

Counter-cases

- Never-worked-this-session failures point to a nono profile / credential setup
  issue, not sleep/wake rot — check the profile first.
- ~/.cache … file exists permission errors are a separate nono cache-dir issue.
