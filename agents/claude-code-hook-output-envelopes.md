---
tags: [agent, claude-code, hooks, integration, envelope, sessionstart, postcompact, additionalcontext, systemmessage]
---

# Claude Code hook output envelopes are asymmetric per event

Claude Code's hook output validator accepts different JSON envelopes for
different events. Two distinctions matter for any plugin that wants its hook
output to actually reach the model.

## Channels

| Envelope                                                                                | Audience                              | Where it lands                       |
|-----------------------------------------------------------------------------------------|---------------------------------------|--------------------------------------|
| `{"hookSpecificOutput": {"hookEventName": "<event>", "additionalContext": "..."}}`      | The model                             | System reminder in the conversation  |
| `{"systemMessage": "..."}`                                                              | The user                              | Transient terminal chip; never seen by the model |

`systemMessage` is for short, ephemeral user-facing notifications (`"plugin
loaded"`, `"3 patterns ingested"`). It is **not** a delivery channel for content
meant to influence the agent.

## Per-event acceptance

| Event             | `hookSpecificOutput.additionalContext` | `systemMessage` |
|-------------------|----------------------------------------|-----------------|
| `SessionStart`    | accepted                               | accepted (no model effect) |
| `UserPromptSubmit`| accepted (required)                    | accepted        |
| `PreToolUse`      | accepted                               | accepted        |
| `PostToolUse`     | accepted                               | accepted        |
| `PostToolBatch`   | accepted                               | accepted        |
| `PostCompact`     | **rejected by validator**              | accepted (no model effect) |

There is no envelope that delivers `PostCompact` output to the model.

## Trigger

You hit this pattern in one of three ways:

- A hook exits 0 and the harness logs `hook_success`, but the model behaves as
  if the payload was never injected.
- `/compact` shows a `Hook JSON output validation failed — (root): Invalid
  input` banner after switching to the `additionalContext` envelope.
- You read the validator's printed schema dump and notice that `SessionStart`
  and `PostCompact` are absent from the documented `hookSpecificOutput`
  variants entirely — yet `SessionStart` works in practice.

## Action

- **For events you want the model to see (`SessionStart`, `PreToolUse`,
  `PostToolUse`, `UserPromptSubmit`, `PostToolBatch`)**: emit
  `{"hookSpecificOutput": {"hookEventName": "<event>", "additionalContext":
  "..."}}`. The `hookEventName` field must match the event being handled — a
  copy-paste mismatch (e.g. `SessionStart` on a `PostCompact` payload) is
  silently dropped by the harness, reproducing the same class of "hook fires
  but model never sees it" bug.
- **For `PostCompact`**: emit no output. The chip-only `systemMessage` channel
  has no audience worth the noise — neither the user (a recurring 30-line
  chip on every compaction) nor the model (it never arrives). Suppress the
  payload entirely and keep the handler retained as an extension point for
  the day Claude Code accepts `additionalContext` here, or for a different
  re-prime mechanism (e.g. opportunistic re-injection on the first
  `PreToolUse` after a session-state marker).
- **Per-event regression test**: assert
  `parsed["hookSpecificOutput"]["hookEventName"]` equals the expected event
  name. The `additionalContext` body assertion alone does not catch the
  copy-paste mismatch class.

**Why:** The validator's error-message schema is incomplete relative to actual
acceptance, so reading the dump and matching it literally produces a working
envelope for some events and a broken one for others. The asymmetry is a
property of the harness, not the plugin — discovered only by exercising every
event source in a live session.

**How to apply:** When building a Claude Code hook plugin, treat the
per-event envelope map above as the source of truth, not the schema dump in
validator errors. Verify each event end-to-end by asking the model in a fresh
session whether it can quote the payload from initial context — `exitCode=0`
and a visible terminal chip are not sufficient evidence of delivery.

## Counter-cases

- **Future harness updates may extend `PostCompact`'s acceptance.** If a
  future Claude Code release accepts `hookSpecificOutput` for `PostCompact`,
  the pattern flips: emit the `additionalContext` envelope and the
  pinned-conventions tier re-seeds after compaction. Re-check the validator
  schema and run a live `/compact` before committing to the assumption.
- **Non-Claude-Code MCP or hook hosts.** Other harnesses (Codex, Gemini,
  Cursor) have their own hook-equivalent channels and validators. This map
  is Claude Code-specific; do not assume the same envelope works elsewhere.
- **`UserPromptSubmit` requires `additionalContext`**, not optional like the
  other accepting events. Returning no output suppresses the prompt
  entirely, which is the intended use case for prompt-rewrite hooks but a
  footgun if you copy-paste a `PostToolUse` handler.
