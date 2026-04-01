# Push an Existing Project to a New GitLab Repo

> Initialize a local project and push it to a newly created GitLab repository.

## Code

```bash
git init --initial-branch=master --object-format=sha1
git remote add origin https://gitlab.com/your-group/your-project.git
git add .
git commit -m "Initial commit"
git push --set-upstream origin master
```

## What It Does

1. `git init --initial-branch=master --object-format=sha1` — Initializes a new Git repository in the current directory. Sets the default branch to `master` (GitLab's default) and uses SHA-1 for object hashing (required for GitLab compatibility, since GitLab does not yet support SHA-256).
2. `git remote add origin <url>` — Links the local repo to the remote GitLab repository. Replace the URL with the one GitLab gives you when you create the project.
3. `git add .` — Stages all files in the directory for the first commit.
4. `git commit -m "Initial commit"` — Creates the initial commit with all staged files.
5. `git push --set-upstream origin master` — Pushes the commit to GitLab and sets `origin/master` as the upstream tracking branch for future `git push` and `git pull` commands.

## Usage

1. Create a new **blank project** in GitLab (no README, no `.gitignore` — the repo must be empty).
2. `cd` into your existing project directory.
3. Run the commands above, replacing the `origin` URL with the one from your new GitLab project.

## Notes

- The remote repo **must be empty**. If GitLab initialized it with a README or any other file, the push will fail due to divergent histories.
- `--initial-branch=master` avoids the default `main` branch name that newer versions of Git use. GitLab projects default to `master` unless your group or instance settings say otherwise.
- `--object-format=sha1` is only necessary if your Git version defaults to SHA-256 (Git 2.42+). On older versions it's a harmless no-op.
- Add a `.gitignore` before running `git add .` to avoid committing build artifacts, `node_modules`, `.env` files, or other things that shouldn't be in the repo.
