---
tags: [github, pull-request, continuous-integration, gh]
---

# GitHub PR CI status — check mergeStateStatus first

"PR is passing CI" requires two conditions: required check contexts are green
AND the PR is mergeable. Check mergeable state first, because CI does not run on
conflicting PRs.

```bash
gh pr view <num> --json mergeable,mergeStateStatus,statusCheckRollup
```

If `mergeStateStatus` is `DIRTY` or `CONFLICTING`, CI hasn't run — regardless of
what individual status contexts show. Fix conflicts first.

Distinguish required CI checks from always-on status contexts: things like
DeepSource, Sleuth, code-coverage gates report a status but are not the actual
CI build. Identify the build/test/lint workflow by name and verify it
specifically.

Only report "CI passing" when (a) `mergeStateStatus` is clean and (b) each
required check has SUCCESS conclusion.
