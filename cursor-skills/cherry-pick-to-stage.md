# Cherry Pick to Stage

> Create a merge branch and cherry-pick the current flattened feature branch's commit to env/stage.

## Adding This Slash Command to Cursor

**Project-level** (recommended—version controlled with your repo):
1. Create `.cursor/commands/` in your project root
2. Copy the **Slash Command** content below into `.cursor/commands/cherry-pick-stage.md`

**Global** (available in all projects):
1. Create `~/.cursor/commands/` if it doesn't exist
2. Save the file there instead

**To run**: Type `/` in Chat or Composer, select `cherry-pick-stage`

**With parameters**: You can pass a ticket number directly:
```
/cherry-pick-stage PROJ-456
```

The command file is just plain markdown—no frontmatter needed.

## Slash Command

Copy everything below this line into `.cursor/commands/cherry-pick-stage.md`:

---

```markdown
# Cherry Pick to Stage

Cherry-pick the current branch's single commit to a stage merge branch.

## Before You Start

1. Fetch latest: `git fetch origin`
2. Verify exactly ONE commit ahead of main:
   ```bash
   git rev-list --count origin/main..HEAD
   ```
   - If 0: Stop. No commits to cherry-pick.
   - If >1: Stop. Squash first with `git rebase -i origin/main`

## Steps

1. Extract the ticket number from the current branch name (e.g., `PROJ-123` from `feature/PROJ-123-description`)

2. Get the commit SHA:
   ```bash
   git log --oneline -1
   ```

3. Create the merge branch from env/stage:
   ```bash
   git checkout -b merge/TICKET-stage origin/env/stage
   ```

4. Cherry-pick the commit:
   ```bash
   git cherry-pick <sha>
   ```

5. If conflicts occur, stop and tell me which files conflict. I'll resolve them, then continue with:
   ```bash
   git add <resolved-files>
   git cherry-pick --continue
   ```

6. Push the merge branch:
   ```bash
   git push -u origin merge/TICKET-stage
   ```

7. Report the result:
   - Branch created: `merge/TICKET-stage`
   - Commit cherry-picked: `<sha>`
   - Next step: Open PR from `merge/TICKET-stage` → `env/stage`
```

---

## Manual Commands

If running without Cursor:

```bash
git fetch origin
[ $(git rev-list --count origin/main..HEAD) -eq 1 ] || echo "Must have exactly 1 commit ahead of main"

TICKET=$(git branch --show-current | grep -oE '[A-Z]+-[0-9]+' | head -1)
SHA=$(git rev-parse --short HEAD)

git checkout -b "merge/${TICKET}-stage" origin/env/stage
git cherry-pick $SHA
git push -u origin "merge/${TICKET}-stage"
```

## Notes

- Assumes ticket format is `JIRA-123` style (uppercase letters, dash, numbers)
- Target branch is `env/stage` on origin
- Squash your feature branch first with `git rebase -i origin/main`
