---
tags: [ci, github-actions, triage, environment, gate, cancelled, timeout, gh, rerun, approval]
---

# GitHub Actions triage

## Environment-gate timeout signature

A job that completes in exactly the workflow's `timeout-minutes` value, with
`conclusion: cancelled` and `steps: []`, is almost always an environment
approval-gate timeout — not a runtime failure.

Three signals together pin the diagnosis:

1. `conclusion = cancelled`.
2. Duration matches a configured timeout to the minute (commonly 15).
3. `steps: []`, or no step past the runner-init phase.

If all three hold, read the workflow file and look for `environment: <name>`.
If present, the gate timed out waiting for an approver. The remedy is approval,
not a code change.

```bash
gh api repos/{owner}/{repo}/actions/runs/<id>/jobs
```

**Why:** `gh pr checks` collapses `cancelled` into `fail`, which makes
approval-gate timeouts indistinguishable from code failures at first glance.
The three-signal triple is a high-precision fingerprint that rules out in-job
failures (image pull, test crash, network) before opening logs.

**How to apply:** Before debugging a "failed" GitHub Actions run as a code
defect, fetch the job JSON and check the triple. Drafts often do not trigger
the approval flow until ready-for-review, which can race with the timeout.

### Counter-cases

- No `environment:` declared: the same signature can mean a concurrency
  cancellation. Check `concurrency:` in the workflow and `gh run list` for a
  newer in-progress run on the same branch.
- Manual cancellation via the UI: identical signature; check the run timeline.
- Self-hosted runner offline: typically the duration is not a round-minute
  match to a configured timeout.

## Re-trigger continuous integration without a new commit

For non-code failures — environment-gate timeouts, flakes, transient
infrastructure — re-run only the failed jobs:

```bash
gh run rerun <run-id> --failed
```

Do not push an empty commit to retrigger continuous integration. Empty commits
mutate branch and pull-request history for no reason.

**Why:** Manual rerun is a first-class primitive in every modern continuous
integration provider. Reach for it whenever the diff is not the cause.

**How to apply:** Combine with the timeout signature above — diagnose the
gate, request approval, then `gh run rerun <id> --failed` to reopen the
approval window. For other providers (CircleCI, Buildkite, GitLab) use the
equivalent rerun primitive rather than an empty commit.

### Counter-cases

- Failure caused by the diff: rerun will not help; fix the code.
- Some `pull_request_target` or reusable-workflow setups behave differently on
  manual rerun. Confirm via `gh run list` after triggering.
