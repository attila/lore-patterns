---
tags: [cli, stdout, stderr, conventions, unix, agent-readiness]
---

# CLI Output Conventions

## Data Output

Data commands (`search`, `list`, `show`, `get`, `export`) write results to
**stdout**.

Status messages, progress indicators, and warnings write to **stderr**.

```rust
# stdout — the purpose of running the command
println!("[1] {}", result.title);
println!("{}", serde_json::to_string(&output)?);

# stderr — diagnostics for the human watching
eprintln!("No results found.");
eprintln!("Searching...");
eprintln!("Warning: Ollama unreachable, falling back to text search.");
```

The dividing line: if the output is the _purpose_ of running the command, it
belongs on stdout. If it helps a human understand what happened, it belongs on
stderr.

## Error Exit Codes

Use distinct exit codes for different failure modes:

- `0` — success
- `1` — general error (bad input, missing resource, runtime failure)
- `2` — usage error (wrong arguments, missing required flags)

Do not exit 0 on failure. The only exception is hook subcommands that must never
break the caller (e.g., Claude Code hooks), and that exception must be
documented in the function's contract.

## Machine-Readable Output

Action commands (`init`, `ingest`, `serve`) that primarily perform side effects
write progress to stderr. Their stdout should be empty or reserved for
structured output a script might consume.

When a command's output is likely consumed by agents or scripts, prefer
structured formats (JSON, one-item-per-line) over decorated prose. If both human
and machine consumers exist, use stderr for the human-friendly version and
stdout for the machine-friendly version.
