# Browser Refresh from Terminal

> Reload the active browser tab via AppleScript, with fallback chain.

## Code

```bash
osascript -e 'tell application "Google Chrome" to reload active tab of window 1' 2>/dev/null \
  || osascript -e 'tell application "Safari" to do JavaScript "location.reload()" in document 1' 2>/dev/null \
  || echo "Please refresh manually (Cmd+R)"
```

## What It Does

This command tries to refresh your browser automatically, attempting Chrome first, then Safari, then giving up gracefully with a message.

- `osascript -e '...'` — Runs AppleScript from the command line. AppleScript is macOS's built-in automation language that can control applications.
- `2>/dev/null` — Redirects error output to nowhere. If Chrome isn't running, the error is silently discarded instead of printing to your terminal.
- `||` — The "or" operator. Only runs the next command if the previous one failed (returned non-zero exit code).

## Usage

Run it directly after a build, or save it as an alias in your shell config (`~/.zshrc` or `~/.bashrc`):

```bash
# Add to ~/.zshrc
alias refresh='osascript -e '\''tell application "Google Chrome" to reload active tab of window 1'\'' 2>/dev/null || osascript -e '\''tell application "Safari" to do JavaScript "location.reload()" in document 1'\'' 2>/dev/null || echo "Please refresh manually (Cmd+R)"'
```

Then use it in your workflow:

```bash
npm run build && refresh
```

## Setup for Safari

Safari blocks JavaScript execution from external sources by default. To enable it:

1. Open Safari
2. Go to Safari → Settings → Advanced
3. Check "Show Develop menu in menu bar"
4. Go to Develop menu → check "Allow JavaScript from Apple Events"

Without this, the Safari fallback will silently fail and you'll see the manual refresh message.

## Notes

- macOS only — `osascript` is an Apple-specific tool
- Chrome must have at least one window open (not just running in the background)
- The quote escaping in the alias (`'\''`) is necessary because the alias itself uses single quotes
- Works great with file watchers, build tools, or any automation where you want to see changes immediately
