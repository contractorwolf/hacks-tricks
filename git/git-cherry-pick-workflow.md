# Git Cherry-Pick Workflow

> Promote a single commit across multiple environment branches cleanly.

## When to Use This

You have long-lived environment branches (`env/dev`, `env/stage`, `main`) and need the same change in each one without messy merge commits or diverging histories.

## The Workflow

### 1. Build Your Feature

Start from main, do your work, then squash everything into one commit:

```bash
git fetch origin
git checkout -b feature/TICKET-123 origin/main

# ... make your changes, commit as you go ...

git rebase -i origin/main   # squash all commits into one
git push -u origin feature/TICKET-123
```

Get the SHA (the commit identifier you'll cherry-pick):

```bash
git log --oneline -1
# abc1234 feat: add user authentication
```

### 2. Cherry-Pick to Each Environment

For each target branch, create a short-lived merge branch and cherry-pick:

```bash
# To stage
git checkout -b merge/TICKET-123-stage origin/env/stage
git cherry-pick abc1234
git push -u origin merge/TICKET-123-stage
# Open PR: merge/TICKET-123-stage → env/stage

# To dev
git checkout -b merge/TICKET-123-dev origin/env/dev
git cherry-pick abc1234
git push -u origin merge/TICKET-123-dev
# Open PR: merge/TICKET-123-dev → env/dev
```

## What These Commands Do

- `git fetch origin` — Downloads the latest state from remote without changing your local branches. Always fetch before branching to ensure you're starting from the latest code.
- `git checkout -b <branch> origin/<base>` — Creates a new branch starting from a remote branch. The `-b` flag creates the branch; without it, you'd just switch to an existing one.
- `git rebase -i origin/main` — Interactive rebase. Opens an editor showing your commits. Change `pick` to `squash` (or `s`) for all but the first commit to combine them into one.
- `git cherry-pick <SHA>` — Copies a commit onto your current branch. Creates a new commit with the same changes but a different commit hash (because the parent is different).
- `git push -u origin <branch>` — Pushes the branch to remote and sets up tracking (`-u`). After this, `git push` and `git pull` work without specifying the remote.

## Handling Conflicts

If cherry-pick hits a conflict:

```bash
# Git will tell you which files conflict
# Edit those files, resolve the conflicts (look for <<<<<<< markers)
git add <resolved-files>
git cherry-pick --continue

# Or, to abandon the cherry-pick:
git cherry-pick --abort
```

## Why Squash First?

- One commit = one cherry-pick = fewer chances for conflicts
- Each environment gets exactly one clean commit
- Easy to revert later (just revert one SHA, not a merge commit)
- The commit content is identical across branches, even though the commit hashes differ

## Naming Convention

- `feature/TICKET-123` — Your working branch where you develop
- `merge/TICKET-123-stage` — Throwaway branch for the PR to stage. The `merge/` prefix signals it's temporary and can be deleted after merging.

## Quick Reference

```bash
# Get SHA of latest commit on a branch
git log --oneline -1 origin/feature/TICKET-123

# Cherry-pick commands
git cherry-pick <SHA>          # apply the commit
git cherry-pick --continue     # after resolving conflicts
git cherry-pick --abort        # bail out entirely

# Clean up after PRs are merged
git branch -d merge/TICKET-123-stage
git push origin --delete merge/TICKET-123-stage
```

## Notes

- Always `git fetch` before starting — stale local refs cause confusion
- The cherry-picked commits have different SHAs than the original because Git commits include their parent reference
- If you need the same change in main too, open a regular PR from your feature branch — don't cherry-pick to main
- Conflicts in one environment don't affect others — each cherry-pick is independent
