---
name: git-workflow
description: Git rules for branches, worktrees, history, and commit series. Use them when you commit, push, rewrite history, or prepare a merge.
---

# Git Workflow

Repo rules (`CLAUDE.md`, `AGENTS.md`, `.claude/rules/`) outrank this skill. This skill outranks memory.

|Rule|Detail|
|---|---|
|Branch before commit|Never commit to `main`.|
|Use a worktree|Before the first edit, run `git worktree add -b <branch> ../<repo>-<topic> main`. Do all edits, builds, and checks in the worktree. Keep the main checkout clean on `main`. Copy gitignored per-machine files (for example `.env.local`) into the new worktree.|
|Edits in the main checkout by mistake|Run `git stash`. Create the worktree. Run `git stash pop` in the worktree. Make sure that the main checkout is clean.|
|No force push on shared branches|Never force-push `main`, a shared branch, or a branch that another person pushes to.|
|Clean history on your own branch|Fold a fix into the commit that it changes. Rewrite the message to the final behavior. Push with `--force-with-lease=<branch>:<expected-sha>`.|
|Before a history rewrite|Create a backup branch. Make sure that `git diff <backup> HEAD` is empty. Compile each commit. Update the commit SHAs in the PR body.|
|No `git add -f`|Ask first.|
|Prune merged branches|After a merge, run `git branch -v`. Delete the `[gone]` local branches.|
|PR edits|Use `gh api repos/{owner}/{repo}/pulls/{number} -X PATCH` to update a PR body.|

## Judge the commit series

Do this before the first push and again before the merge. State the verdict in one line.

|Problem|Action|
|---|---|
|A later commit reverses the decision of an earlier commit|Squash or reorder|
|One commit bundles changes that you can revert independently|Split it|
|A commit has a different subject from the branch|Move it to a different branch|

Keep the history when each commit is a separate lever and the fixed mechanism is unverified. A series that you can bisect is better than a tidy series.
