# Cherry Pick to Dev

> Create a merge branch and cherry-pick the current flattened feature branch's commit to env/dev.

## Adding This Slash Command to Cursor

**Project-level** (recommended—version controlled with your repo):
1. Create `.cursor/commands/` in your project root
2. Copy the **Slash Command** content below into `.cursor/commands/cherry-pick-dev.md`

**Global** (available in all projects):
1. Create `~/.cursor/commands/` if it doesn't exist
2. Save the file there instead

**To run**: Type `/` in Chat or Composer, select `cherry-pick-dev`

**With parameters**: You can pass a ticket number directly:
```
/cherry-pick-dev PROJ-456
```

The command file is just plain markdown—no frontmatter needed.

## Slash Command

Copy everything below this line into `.cursor/commands/cherry-pick-dev.md`:

---

```markdown
# Cherry Pick to Dev

Cherry-pick the current branch's single commit to a dev merge branch.

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

3. Create the merge branch from env/dev:
   ```bash
   git checkout -b merge/TICKET-dev origin/env/dev
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
   git push -u origin merge/TICKET-dev
   ```

7. Report the result:
   - Branch created: `merge/TICKET-dev`
   - Commit cherry-picked: `<sha>`
   - Next step: Open PR from `merge/TICKET-dev` → `env/dev`
```

---

## Manual Commands

If running without Cursor:

```bash
git fetch origin
[ $(git rev-list --count origin/main..HEAD) -eq 1 ] || echo "Must have exactly 1 commit ahead of main"

TICKET=$(git branch --show-current | grep -oE '[A-Z]+-[0-9]+' | head -1)
SHA=$(git rev-parse --short HEAD)

git checkout -b "merge/${TICKET}-dev" origin/env/dev
git cherry-pick $SHA
git push -u origin "merge/${TICKET}-dev"
```

## Notes

- Assumes ticket format is `JIRA-123` style (uppercase letters, dash, numbers)
- Target branch is `env/dev` on origin
- Squash your feature branch first with `git rebase -i origin/main`
