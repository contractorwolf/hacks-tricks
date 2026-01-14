# Git Browse Alias

**Open your current repo's GitHub/GitLab page directly from the terminal.**

## The Problem

Most `git browse` aliases break if you use SSH remotes (`git@github.com:...`) because the macOS `open` command doesn't recognize that format.

## The Solution

This alias uses `sed` to transform your SSH remote URL into a clickable HTTPS link on the fly.

## Installation

Run this single line in your terminal:

```bash
git config --global alias.browse '!f() { url=$(git remote get-url origin | sed -E "s|git@([^:]+):(.+)\.git|https://\1/\2|"); open "$url"; }; f'
```

## Usage

```bash
git browse
```

Your default browser opens to the repo's landing page.

## How It Works

| Component | Purpose |
|-----------|---------|
| `git remote get-url origin` | Pulls your primary remote URL |
| `sed -E "s\|git@([^:]+):(.+)\.git\|https://\1/\2\|"` | Converts SSH format to HTTPS |
| `open "$url"` | Launches your default browser (macOS) |

### Regex Breakdown

```
git@github.com:user/repo.git
    └───┬────┘ └────┬────┘
        │           │
        \1          \2
        ↓           ↓
https://github.com/user/repo
```

## Caveats

- **macOS only** — uses the `open` command. On Linux, replace with `xdg-open`. On Windows (Git Bash), use `start`.
- **Smart quotes** — if copying from formatted text, ensure quotes are plain (`'` and `"`) not curly.
- **Assumes `.git` suffix** — if your remote doesn't end in `.git`, adjust the regex accordingly.
