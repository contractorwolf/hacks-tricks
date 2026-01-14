# Git Cherry-Pick Workflow for Multi-Environment Deploys

**Promote a single squashed commit cleanly across environment branches.**

## The Problem

You have multiple long-lived environment branches (`env/dev`, `env/stage`, `master`/`main`) and need to land the same change in each without merge commits piling up or history diverging.

## The Solution

1. Squash your feature to **one commit** on your feature branch
2. **Cherry-pick** that single SHA into each environment branch via short-lived merge branches
3. Open PRs that land exactly one commit per environment

## The Workflow

### 1. Build Your Feature (Squashed to 1 Commit)

```bash
git fetch origin
git checkout -b feature/UALS-100 origin/master

# ... do your work, commit as needed ...

git rebase -i origin/master   # squash everything into 1 commit
git push -u origin feature/UALS-100
```

Grab the SHA you'll be cherry-picking:

```bash
git log --oneline -1 origin/feature/UALS-100
# abc1234 feat: add user authentication
```

### 2. Cherry-Pick to Stage

```bash
git checkout -b merge/UALS-100-stage origin/env/stage
git cherry-pick abc1234
# resolve conflicts if any, then: git cherry-pick --continue
git push -u origin merge/UALS-100-stage
```

Open PR: `merge/UALS-100-stage` → `env/stage`

### 3. Cherry-Pick to Dev

```bash
git checkout -b merge/UALS-100-dev origin/env/dev
git cherry-pick abc1234
# resolve conflicts if any
git push -u origin merge/UALS-100-dev
```

Open PR: `merge/UALS-100-dev` → `env/dev`

## Why This Works

| Benefit | Explanation |
|---------|-------------|
| **Clean history** | Each environment branch gains exactly 1 commit |
| **Easy rollback** | Revert a single SHA, not a merge commit |
| **Consistent diffs** | Same change, same SHA (content-addressable) |
| **Conflict isolation** | Fix conflicts per-environment without polluting others |

## Visual Flow

```
master ────●──────────────────────────►
           │
           └─► feature/UALS-100 (squash) ──● abc1234
                                            │
           ┌────────────────────────────────┤
           │                                │
           ▼                                ▼
env/stage ──● cherry-pick abc1234    env/dev ──● cherry-pick abc1234
```

## Tips

- **Always fetch first** — ensures you branch from the latest remote state
- **Use `merge/` prefix** — signals these are throwaway PR branches, not long-lived
- **Squash before cherry-picking** — one commit = one cherry-pick = fewer conflicts
- **Same SHA, different parents** — the cherry-picked commits will have different commit hashes but identical content hashes

## Quick Reference

```bash
# Get the SHA
git log --oneline -1 origin/feature/TICKET

# Cherry-pick with conflict handling
git cherry-pick <SHA>
git cherry-pick --continue   # after resolving
git cherry-pick --abort      # to bail out
```
