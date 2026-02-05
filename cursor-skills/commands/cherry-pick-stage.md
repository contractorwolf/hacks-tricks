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

1. Extract the ticket number from the current branch name (e.g., `PROJ-123` from `feature/PROJ-123-description`). If a ticket was passed as a parameter, use that instead.

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
