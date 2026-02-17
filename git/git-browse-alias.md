# Git Browse Alias

> Open your repo's GitHub/GitLab page from the terminal.

## Code

```bash
git config --global alias.browse '!f() { url=$(git remote get-url origin | sed -E "s|git@([^:]+):(.+)\.git|https://\1/\2|"); open "$url"; }; f'
```

## Usage

```bash
git browse
```

Opens your default browser to the repo's main page.

## What It Does

This creates a global Git alias that converts your SSH remote URL to an HTTPS URL and opens it in a browser.

- `git config --global alias.browse '...'` — Creates a Git alias available in all repos. After running this once, `git browse` becomes a valid command.
- `!f() { ... }; f` — The `!` tells Git to run this as a shell command, not a Git subcommand. The `f() { }; f` pattern defines and immediately calls a function (needed for multi-step commands).
- `git remote get-url origin` — Gets the URL of your `origin` remote (where you cloned from).
- `sed -E "s|...|...|"` — Transforms the URL using a regular expression. The `-E` flag enables extended regex syntax.
- `open "$url"` — macOS command to open a URL in the default browser.

## Why the Transformation?

Most repos are cloned via SSH for authentication:

```
git@github.com:username/repo.git
```

But browsers need HTTPS URLs:

```
https://github.com/username/repo
```

The `sed` regex captures the host (`github.com`) and path (`username/repo`), then reassembles them as HTTPS.

## Breaking Down the Regex

```
s|git@([^:]+):(.+)\.git|https://\1/\2|

s|...|...|     — Substitute pattern with replacement
git@          — Match literal "git@"
([^:]+)       — Capture group 1: everything until the colon (the host)
:             — Match literal colon
(.+)          — Capture group 2: everything after colon until .git (the path)
\.git         — Match literal ".git" (backslash escapes the dot)
https://\1/\2 — Replacement: HTTPS URL using captured groups
```

## Platform Variants

```bash
# Linux
git config --global alias.browse '!f() { url=$(git remote get-url origin | sed -E "s|git@([^:]+):(.+)\.git|https://\1/\2|"); xdg-open "$url"; }; f'

# Windows (Git Bash)
git config --global alias.browse '!f() { url=$(git remote get-url origin | sed -E "s|git@([^:]+):(.+)\.git|https://\1/\2|"); start "$url"; }; f'
```

## Notes

- Run the config command once — the alias persists in your `~/.gitconfig`
- Works with GitHub, GitLab, Bitbucket, or any host using standard SSH URL format
- If your remote doesn't end in `.git`, remove `\.git` from the regex
- If you use HTTPS remotes already, the regex won't match and `open` will try to open the SSH URL (which will fail) — but if you use HTTPS, you probably don't need this alias
