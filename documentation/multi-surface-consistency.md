---
tags: [documentation, consistency, grep, identifiers, rendered, derived, contracts, single-source-of-truth]
---

# Multi-surface documentation consistency

Treat every documentation surface that quotes an identifier as one update set.
Before declaring a documentation change complete, grep the repository for each
identifier you touched and update every match.

## Trigger

About to mark a documentation change done after editing what looks like the
canonical source — typically a change that introduces, renames, or modifies an
identifier (parameter name, enum value, contract field, public type, CLI flag,
configuration key).

## Action

- Run `git grep -nF '<identifier>'` for every identifier the change touches.
- Update each hit that quotes, mirrors, or describes the identifier — not just
  the file you opened first.
- Re-grep after the final pass to confirm nothing remains stale.
- Land all hits in the same commit, or in a tightly contiguous series.

## Surfaces commonly missed

- Static-site content collections (Astro, Next.js MDX, Docusaurus, Hugo data
  files).
- API contract JSONs that mirror Swagger or OpenAPI YAMLs.
- Sidebar and navigation manifests.
- Code-generated TypeScript types, Python stubs, or protobuf bindings.
- README and changelog tables.
- Architecture diagrams driven by JSON or YAML data.

**Why:** Single-source-of-truth is the ideal; in practice most repositories
carry rendering and derivation layers. Stale rendered documentation is silently
wrong until a reader clicks through. The cost of one grep is far below the cost
of a follow-up commit and re-review.

**How to apply:** Run the grep before the final commit, not after review
feedback. If the repository regenerates a tree in continuous integration, still
grep to confirm the regeneration ran before merge.

## Counter-cases

- Single-README repositories: the pattern collapses to a no-op.
- Externally-hosted documentation (Notion, Confluence, dashboards): out of scope
  for the in-repository grep; raise a follow-up checklist item if the repository
  references external contracts.
