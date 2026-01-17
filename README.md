# hacks&tricks

Command-line tricks and workflow hacks for engineers. Each page is a self-contained reference with the code upfront and explanations that teach.

## Contents

### [browser-refresh.md](browser-refresh.md)
Reload your browser's active tab from the terminal using AppleScript. Tries Chrome, falls back to Safari, then prints a manual instruction. Useful for rapid iteration during frontend development.

### [git-browse-alias.md](git-browse-alias.md)
Open your repo's GitHub/GitLab page directly from the terminal. Creates a global `git browse` command that converts SSH remote URLs to HTTPS and opens them in your default browser.

### [git-cherry-pick-workflow.md](git-cherry-pick-workflow.md)
A workflow for promoting changes across multiple environment branches (`dev`, `stage`, `main`) using cherry-pick. Keeps history clean with one commit per environment and avoids merge commit clutter.

## Adding New Pages

Follow the structure in [CLAUDE.md](CLAUDE.md):

1. Title with one-line summary
2. Code block immediately
3. "What It Does" breakdown
4. Usage examples
5. Notes for caveats and edge cases

Keep it code-forward but explanatory enough for someone unfamiliar with the tools.
