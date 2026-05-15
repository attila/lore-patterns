---
tags: [documentation, grammar, terminology, writing, style]
---

# Documentation Terminology Standards

## Full words over abbreviations

Use the complete word in prose, not the informal shorthand. Abbreviations are
acceptable only in code identifiers, CLI flags, and file paths.

| Use           | Not                                                      |
| ------------- | -------------------------------------------------------- |
| deduplication | dedup                                                    |
| configuration | config (in prose)                                        |
| repository    | repo                                                     |
| information   | info                                                     |
| documentation | docs (when referring to documents, not the `docs/` path) |
| arguments     | args (in prose)                                          |

**Why:** Abbreviations are sloppy tech-talk that erodes reader trust in
technical documentation. Grammatical precision signals trustworthy technical
content. Code identifiers (`--config`, `dedup_file_path`) keep their original
form — this convention applies to prose only.

## Emphasis conventions

- _Italics_ for rhetorical emphasis — drawing attention to a word or phrase
- **Bold** for structural weight — section labels, defined terms, headings
  within prose
- Do not use bold for emphasis or italics for structural labels

## Spelling

Use British English consistently: behaviour, organisation, tokeniser,
normalised, artefact. Not American English variants.

## Aside formatting

Use plain blockquotes with a bold question-as-label for asides and callouts.
This format renders well across all markdown renderers (GitHub, mdbook, man
pages, terminals) without depending on platform-specific extensions.

```markdown
> **Why not semantic search?** You might expect semantic search to bridge this
> gap, but hook-injected queries are typically short...
```

Do not use GitHub-flavoured admonitions (`> [!NOTE]`) — they do not render in
mdbook or man pages. Do not use collapsible `<details>` blocks for content that
most readers need.

## How to apply

When writing or reviewing any markdown documentation in this project, apply
these conventions. When in doubt, prefer the formal word over the abbreviation
and check for British English spelling.
