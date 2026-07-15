# Auto-save Claude-in-Chrome screenshots to disk

A drop-in Claude Code hook that writes every browser screenshot to `docs/screenshots/` as a real image file, with a descriptive, model-chosen filename.

## Why this exists

The Claude-in-Chrome `computer` tool exposes a `save_to_disk` option on its `screenshot` action, but it's a **no-op** — the flag lives in the tool schema and is never read by the handler, so no file is ever written ([anthropics/claude-code#66077](https://github.com/anthropics/claude-code/issues/66077)). Screenshots come back only as an inline base64 image that the model sees as a picture, not as text bytes it can save. So "ask the model to save the base64" can't work — the model never has the base64 as text.

A `PostToolUse` hook sits at the right layer: Claude Code fires it **after** the tool returns but **before** the result is decoded for the model, piping the raw tool-result JSON — base64 image and all — to the hook on stdin. The hook pulls the base64 out and writes the file itself, deterministically, on every screenshot.

## Filenames

The hook can't see the picture, so it can't describe what's in it — but the model can. The naming is resolved in this order:

1. **Model-provided label (preferred).** Just before taking a screenshot, the model writes one line of description to `.claude/hooks/.next-screenshot-label`. The hook reads it, uses it, and deletes it (consume-once), so the label only applies to the next shot. Example → `chatgpt-lendingtree-ca-purchase-rates-widget-1784152944510.jpg`.
2. **Derived from tab context.** With no label, the hook parses the active tab's `site` + page `title` out of the tab-context text in the payload. Example → `linkedin-feed-linkedin-1784152967408.jpg`.
3. **Fallback.** Neither available → `screenshot-1784152967408.jpg`.

Each name ends in a 13-digit **milliseconds-since-epoch** timestamp, so the exact capture time is recoverable (`date -r $((ms/1000))`, or `datetime.fromtimestamp(ms/1000)`). Same-name/same-ms captures get `-01`, `-02`, … so nothing is ever overwritten.

To label a shot, the model runs this immediately before the screenshot tool call:

```sh
printf 'ChatGPT — LendingTree CA purchase rates widget' > .claude/hooks/.next-screenshot-label
```

## Requirements

- `jq` and `base64` on `PATH` (both ship with macOS; `jq` is `apt install jq` / `brew install jq` elsewhere).
- The Claude-in-Chrome extension connected (this hook only reacts to its screenshots).

### Windows

It works on Windows too — Claude Code runs shell-form `command` hooks through **Git Bash by default**, and expands `$CLAUDE_PROJECT_DIR` itself, so the config is unchanged. Two prerequisites:

- **Git Bash installed.** Everything the script needs — `base64`, `sed`, `grep`, `tr`, and GNU `date` (so `date +%s%3N` yields real milliseconds) — ships with it. The `python3`/`perl` fallback in `epoch_ms` never fires there.
- **`jq` installed separately** — it is *not* bundled with Git Bash. `winget install jqlang.jq`, or put `jq.exe` on `PATH`.

Keep the script's line endings as **LF**, not CRLF, or Git Bash chokes on the `\r`. Add `*.sh text eol=lf` to `.gitattributes` so it stays LF across checkouts. If a machine has neither Git Bash nor `jq`, port the logic to PowerShell (native `[Convert]::FromBase64String` + `ConvertFrom-Json`, no external tools) and invoke it as `"command": "powershell -File \"$CLAUDE_PROJECT_DIR/.claude/hooks/extract-screenshot.ps1\""`.

## Setup

1. **Save the script** below as `.claude/hooks/extract-screenshot.sh` in your project, then make it executable:

   ```sh
   chmod +x .claude/hooks/extract-screenshot.sh
   ```

2. **Register the hook** in `.claude/settings.local.json` (personal, not committed) — or `.claude/settings.json` to share it with the repo. Merge this `hooks` block in alongside any existing keys:

   ```json
   {
     "hooks": {
       "PostToolUse": [
         {
           "matcher": "mcp__claude-in-chrome__computer",
           "hooks": [
             {
               "type": "command",
               "command": "\"$CLAUDE_PROJECT_DIR/.claude/hooks/extract-screenshot.sh\""
             }
           ]
         }
       ]
     }
   }
   ```

3. **Activate it.** Claude Code snapshots hooks at startup for safety. If a new session doesn't pick it up, restart Claude Code or review/approve it via the `/hooks` menu.

4. **Test:** take any browser screenshot; a descriptively named `.jpg` appears in `docs/screenshots/`.

## The script

```bash
#!/usr/bin/env bash
# PostToolUse hook for mcp__claude-in-chrome__computer (screenshot / zoom).
#
# The Chrome tool's own `save_to_disk` flag is a documented no-op — it never
# writes a file. But the raw MCP tool result still carries the screenshot as a
# base64 image at THIS layer (before the harness decodes it into a picture for
# the model). So we pull the base64 out of stdin and write the image ourselves.
set -uo pipefail

input="$(cat)"

proj="${CLAUDE_PROJECT_DIR:-$(pwd)}"
outdir="$proj/docs/screenshots"
mkdir -p "$outdir"

# Cheap, jq-free gate: skip actions that don't return an image (clicks, types,
# scrolls). Done BEFORE the dependency check so ordinary clicks stay silent even
# on a box that's missing jq — only image captures trigger the checks below.
if printf '%s' "$input" | grep -qE '"action"[[:space:]]*:'; then
  printf '%s' "$input" | grep -qE '"action"[[:space:]]*:[[:space:]]*"(screenshot|zoom)"' || exit 0
fi

# Preflight: this hook needs jq and base64. If either is missing, the screenshot
# still succeeded in-session but can't be written to disk — say exactly what's
# missing and how to fix it for this OS, then stop. (A missing bash/Git Bash
# can't be reported here: the script wouldn't run at all — see the doc.)
missing=""
command -v jq >/dev/null 2>&1 || missing="$missing jq"
command -v base64 >/dev/null 2>&1 || missing="$missing base64"
if [ -n "$missing" ]; then
  os="$(uname -s 2>/dev/null || echo unknown)"
  {
    echo "extract-screenshot: screenshot NOT saved — missing tool(s):${missing}"
    echo "  (the capture succeeded in-session; only the on-disk copy failed)"
    case "$os" in
      Darwin) echo "  Fix (macOS):   brew install jq        # base64 is built into macOS" ;;
      Linux) echo "  Fix (Linux):   sudo apt-get install -y jq   # or dnf/yum install jq; base64 is in coreutils" ;;
      MINGW* | MSYS* | CYGWIN*)
        echo "  Fix (Windows/Git Bash):  winget install jqlang.jq   # or put jq.exe on PATH"
        echo "                           base64 ships with Git Bash — reinstall Git for Windows if it's gone"
        ;;
      *) echo "  Fix: install the tool(s) above and make sure they're on PATH for the hook's shell" ;;
    esac
  } >&2
  exit 2
fi

# Confirm the action once more with jq (now known to be present); used below.
action="$(printf '%s' "$input" | jq -r '.tool_input.action // empty' 2>/dev/null)"

# Grab the longest base64-looking string anywhere in the payload = the image.
b64="$(printf '%s' "$input" \
  | jq -r '[.. | strings | select(test("^[A-Za-z0-9+/=\r\n]{500,}$"))] | max_by(length) // empty' 2>/dev/null)"

if [ -z "$b64" ]; then
  echo "extract-screenshot: no base64 image in tool output (action=${action:-?})" >&2
  exit 0
fi

# Pick an extension from any mime type present; default jpg (this tool emits jpeg).
mime="$(printf '%s' "$input" \
  | jq -r '[.. | objects | (.mimeType? // .media_type?)] | map(select(type == "string")) | .[0] // "image/jpeg"' 2>/dev/null)"
case "$mime" in
  *png) ext=png ;;
  *) ext=jpg ;;
esac

# Milliseconds since the Unix epoch, portably. GNU date has %N; BSD date (macOS)
# does not, so fall back to python3, then perl, then seconds*1000.
epoch_ms() {
  local ms
  ms="$(date +%s%3N 2>/dev/null)"
  case "$ms" in
    '' | *[!0-9]*) ;; # unsupported (BSD leaves a literal 'N') — try fallbacks
    *) printf '%s' "$ms"; return ;;
  esac
  if command -v python3 >/dev/null 2>&1; then
    python3 -c 'import time; print(int(time.time() * 1000))'
  elif command -v perl >/dev/null 2>&1; then
    perl -MTime::HiRes=time -e 'printf "%d", time() * 1000'
  else
    printf '%s000' "$(date +%s)"
  fi
}

# Turn free text into a safe, compact filename slug.
slugify() {
  printf '%s' "$1" \
    | tr '[:upper:]' '[:lower:]' \
    | tr -c 'a-z0-9' '-' \
    | sed -E 's/-+/-/g; s/^-//; s/-$//' \
    | cut -c1-60
}

# Descriptive name: prefer a label the model dropped just before the shot
# (consume-once), else derive "site page-title" from the tab context in the
# payload, else fall back to "screenshot".
labelfile="$proj/.claude/hooks/.next-screenshot-label"
desc=""
if [ -s "$labelfile" ]; then
  desc="$(cat "$labelfile")"
  rm -f "$labelfile"
else
  text="$(printf '%s' "$input" | jq -r '[.. | .text? // empty] | join("\n")' 2>/dev/null)"
  active="$(printf '%s' "$text" | grep -oE 'Executed on tabId: [0-9]+' | grep -oE '[0-9]+' | head -1)"
  line=""
  [ -n "$active" ] && line="$(printf '%s' "$text" | grep -E "tabId ${active}:" | head -1)"
  [ -z "$line" ] && line="$(printf '%s' "$text" | grep -oE 'tabId [0-9]+:.*' | head -1)"
  title="$(printf '%s' "$line" | sed -nE 's/.*: "([^"]*)".*/\1/p')"
  host="$(printf '%s' "$line" | grep -oE 'https?://[^ )]+' | head -1 | sed -E 's#https?://([^/]+).*#\1#; s/^www\.//')"
  desc="${host%%.*} $title"
fi

slug="$(slugify "$desc")"
[ -z "$slug" ] && slug="screenshot"

ts="$(epoch_ms)"
# Milliseconds-since-epoch stamp; -01, -02, … guards the rare same-ms clash.
out="$outdir/${slug}-${ts}.$ext"
n=1
while [ -e "$out" ]; do
  out="$outdir/${slug}-${ts}-$(printf '%02d' "$n").$ext"
  n=$((n + 1))
done
printf '%s' "$b64" | tr -d '\r\n' | base64 -d > "$out" 2>/dev/null

if [ -s "$out" ]; then
  bytes="$(wc -c < "$out" | tr -d ' ')"
  echo "extract-screenshot: saved $out ($bytes bytes, $mime)" >&2
else
  rm -f "$out"
  echo "extract-screenshot: base64 decode produced empty file" >&2
fi
exit 0
```

## When a dependency is missing

The hook fails loudly, never silently. Before it tries to write anything it checks for `jq` and `base64`; if either is absent it prints what's missing and the fix for the detected OS, then exits `2` (which surfaces the message back into the Claude Code session). The screenshot itself still succeeded in-memory — only the on-disk copy is skipped. Example on Windows:

```
extract-screenshot: screenshot NOT saved — missing tool(s): jq
  (the capture succeeded in-session; only the on-disk copy failed)
  Fix (Windows/Git Bash):  winget install jqlang.jq   # or put jq.exe on PATH
                           base64 ships with Git Bash — reinstall Git for Windows if it's gone
```

The dependency check runs *after* the image-action gate, so ordinary clicks/scrolls never trigger the warning — only actual screenshot/zoom captures do.

**The one case the hook can't self-report is a missing `bash`/Git Bash**, because then the script never executes at all. That surfaces instead as a Claude Code hook error like "command not found" or a failure to spawn the hook. If you see that on Windows, install Git for Windows (which provides Git Bash); on Linux/macOS bash is always present.

## How it works, step by step

1. `input="$(cat)"` — reads the tool-result JSON that Claude Code pipes to the hook on stdin.
2. Resolves the project root from `$CLAUDE_PROJECT_DIR` (set by Claude Code) and ensures `docs/screenshots/` exists.
3. **Action gate (jq-free):** a `grep` on the raw payload skips non-image actions (clicks, types, scrolls) immediately — deliberately *before* the dependency check so ordinary actions stay silent even without `jq`.
4. **Dependency preflight:** verifies `jq` and `base64`; if either is missing, prints the missing tool(s) + an OS-specific fix and exits `2` (see "When a dependency is missing").
5. **Base64 grab:** `.. | strings` walks *every* string in the payload, keeps ones that look like base64 and are ≥500 chars, and takes the longest — that's the image, wherever it sits in the JSON. This survives payload-shape changes (today it's at `tool_response[].source.data`).
6. **Mime → extension:** recursively finds any `mimeType`/`media_type`, picks `png` vs `jpg` (defaults to `jpg`, which is what the tool emits).
7. **Name:** consume the `.next-screenshot-label` file if present; otherwise parse `site` + page `title` from the tab context; slugify the result.
8. **Collision-proof filename:** `<slug>-<epoch-ms>` (13-digit, recoverable to the exact capture time), with `-01`, `-02`, … on the rare same-ms clash.
9. **Decode & write:** strips newlines, `base64 -d` into the file, then confirms it's non-empty (cleans up and reports otherwise). The `>&2` lines surface as the hook's feedback.

## Notes

- Output lives under `docs/screenshots/` relative to the project root — change `outdir` to relocate it.
- Reacts only to `mcp__claude-in-chrome__computer`; other tools are untouched.
- The hook never modifies what the model sees — it only writes a side file.
- The label is consume-once: if you set one but the next capture never happens, it lingers and would name whatever screenshot comes next. Harmless, but worth knowing.
