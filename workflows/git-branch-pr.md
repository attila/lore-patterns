---
tags: [conventions, git, commit, push, branch, pull-request, merge, ownership]
applies_when:
  bash_command_starts_with: [git, gh]
---

# Workflow conventions

## Git workflow

These apply when no overriding project-level conventions are in place.

- Feature branches only, never push directly to `main`, except for the initial
  commit of a new repository.
- New branches are created from `main`.
- Squash merges to main when available.
- Require branch to be up to date before merging even if not enforced by
  protection rules.
- Use short commit-type prefixes for branch names: `feat/`, `fix/`, `refactor/`,
  `doc/`, `ci/`, `deps/` — NOT `feature/`, `bugfix/`, `docs/`, etc.
  - **Why:** Project conventions typically align branch prefixes with the
    conventional commit message types. Only these six are allowed: feat, fix,
    ci, deps, refactor, doc.
  - **How to apply:** When creating branches, use `feat/XYZ-123/description` not
    `feature-XYZ-123-description`. Same for all other types.

## Commits

- Commits always GPG-signed automatically via git config, halt and ask if GPG
  configuration is missing.
- Conventional commit messages: `feat:`, `fix:`, `refactor:`, `chore:`, `doc:`,
  `deps:`, `test:`, `ci:`, and `BREAKING CHANGE:` (for libraries)
  - **Why:** Project convention restricts the type vocabulary to a minimal,
    unambiguous set.
  - **How to apply:**
    - `docs` → use `doc`
    - `style` → use `refactor`
    - `perf` → use `refactor`
    - No other prefixes are permitted
- The first line is short and descriptive, 70-characters or less, followed by a
  blank line.
- Body is optional but should be present if the commit is not a trivial fix. The
  length of the body is proportional to the complexity of the change in the
  commit.
- Brevity with completeness is preferred over lengthy explanations.
- Body copy is always wrapped at 80-characters. Multiple paragraphs have empty
  lines between them.

## Pull requests

- When opening a PR, always create a draft PR first unless instructed otherwise.
- Titles are short but descriptive.
- PR descriptions are always updated with the latest changes when pushing new
  commits.
- Once a PR is merged, fetch with prune, and delete the branch and worktree (if
  present) locally.
- Before writing a PR description, read and follow
  `workflows/pr-description-templates.md`.

## Merge ownership

Only the repository owner merges pull requests. Never merge a PR or push directly to `main`
unless the owner explicitly instructs you to do so. When in doubt, leave the PR as a draft and
ask.

**Why:** During an early session, an agent pushed commits directly to `main`, which auto-closed
a draft PR the owner wanted to review and merge themselves. The owner lost control over the
merge and the review workflow.

**How to apply:** When your work is ready, open or update a draft PR and notify the owner. Do
not mark it ready for review, merge it, or push to `main`. If you are already on `main` with
new commits, create a branch from HEAD before pushing, and only push the feature branch.

## Pushing

- Use `git push origin HEAD` instead of `git push` to only push the current
  branch. Plain `git push` can attempt to push `main` if tracking is set up,
  causing rejected errors and unwanted side effects.
- Use `git push --set-upstream origin HEAD` to set the upstream branch for the
  current branch. It is easier to clean up after a merge than to delete the
  remote branch.

### Pushing to branches with open pull requests

- Always check for a fresh base branch and rebase before every push, not just
  the initial one. Never skip this even if the branch was just created from main
  — the base branch may have moved.
- Before every `git push origin HEAD`, run `git fetch origin {base}` and check
  `git log HEAD..origin/{base}`. If there are new commits, rebase before
  pushing. However, once a PR is marked ready for review (no longer draft), only
  rebase if it does not rewrite commits that reviewers have already seen — use
  merge instead in that case, or coordinate with the author.
- Never rebase and force push to a PR if it rewrites history behind existing
  reviews/comments.
- Always check for fresh main and rebase before every push, not just the initial
  one
  - We often work in fast-moving repos where branches get outdated quickly.
    Pushing without rebasing wastes CI time on merge conflicts
