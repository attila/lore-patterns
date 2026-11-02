---
tags: [documentation, comments, specs, contracts, stale, tickets, references, readme, setup, environment, openapi, yaml]
---

# Prose that stays true

Committed prose goes stale or misleads when it states more than a lasting rule:
a fact that changes on its own schedule, or a detail of one person's setup. The
common sources follow.

## Don't restate what the code shows

In comments, manifests and documentation, avoid counts and lists that mirror the
code ("one bundle, one entrypoint", "three services, two in TypeScript"). They
go stale the next time the shape changes, and a packaging-only edit then forces
a prose edit, sometimes a redeploy. Describe behaviour instead, and state a rule
once in the document that owns it, pointing to it from elsewhere.

## Keep release context out of contracts

Wire-format specs, OpenAPI files and schemas state the contract; the narrowed
enum or literal is the documentation. Release-milestone qualifiers ("beta scope:
X only") outlive the milestone and turn into stale comments. Put release context
in the pull request, the epic or the changelog. Leave qualifiers a surface
already carries alone, but don't add new ones.

## Deferred-work pointers clean themselves up

When a committed file says "tracked in TICKET" for deferred work, add an
acceptance criterion to that ticket: remove the pointer when it ships. Otherwise
the fixed concern leaves a reference to a closed ticket that misleads later
readers.

## State requirements, not one person's setup

Agents work inside one person's environment and see its variable names, paths
and credential plumbing. Committed documentation states the requirement
generically ("a token with `read:packages`, SSO-authorised"), not the local
variable or keychain mechanics that happen to provide it, which readers copy as
if they were the project's convention.
