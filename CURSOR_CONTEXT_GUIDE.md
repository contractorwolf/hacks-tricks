# Cursor Context Management Guide

> Strategies for monitoring, controlling, and optimizing AI context in Cursor IDE and CLI.

## 1. Monitoring Context Size

Cursor provides several ways to track how much context the AI is using.

### In the UI (Chat & Composer)

**Context Pills**: In the Chat (`Cmd+L`) or Composer (`Cmd+I`) pane, look for context pills at the bottom of the input area. Click them to see files and folders currently attached.

**Token Count**: Cursor previously displayed a circular indicator showing token usage (e.g., "45k/200k"). As of version 2.2.44+, this indicator was removed. Users have requested it be restored.

**Workaround via Rules**: Add this to `Cursor Settings > General > Rules for AI`:

```
At the end of every response, provide a brief summary of the files currently in your context window and an estimate of the total token usage.
```

**Dashboard**: Check `Dashboard > Usage` for overall token consumption. If you have Usage Based Pricing enabled, also check `Dashboard > Billing`.

### In the CLI

When running `cursor-agent`, context information appears in the session metadata. Use `agent ls` to see indexed sessions and their sizes.

## 2. Clearing and Resetting Context

Starting fresh eliminates "hallucination loops" and irrelevant history buildup.

**New Thread**: Use `Cmd+N` (macOS) or `Ctrl+N` (Windows/Linux) with the Chat or Composer pane focused. This archives the current thread and starts a fresh session.

**New Tab**: Use `Cmd+T` (macOS) or `Ctrl+T` (Windows/Linux) to create a new parallel conversation tab. Tabs can run simultaneously and aren't dependent on previous requests.

**Composer Reset**: When a Composer session accumulates too many files or changes, Accept or Reject all pending changes, then start a new Composer instance.

**The "5-Prompt Rule"**: If a bug isn't resolved within ~5 prompts, the context is likely polluted. Copy your goal, hit `Cmd+N`, and restart with only the essential files referenced.

## 3. Forking Context (Branching)

Forking preserves a conversation checkpoint, letting you try multiple approaches without starting over.

### Forking in the UI

1. Hover over a previous message in Chat history
2. Click the "Duplicate" or branching icon
3. This creates a new thread identical up to that point

You can now pursue different implementation paths from the same starting point.

### Forking in the CLI

```bash
# List all previous sessions
agent ls

# Resume the most recent conversation
agent resume

# Resume a specific session by ID
cursor-agent --resume="<session-id>"

# Alternative slash command within a session
/resume
```

There's no dedicated `/fork` command, but resuming a session and changing your `@` file references achieves the same effect.

## 4. Compressing Context

Compression condenses conversation history to free up tokens without losing everything.

### Manual Compression

**In Chat**: Type `/compress` to summarize the current conversation into a condensed message and clear the detailed history.

**Alternative**: Use `/summarize` for a similar effect (released in Cursor 1.6).

### Automatic Compression

When context reaches 100% capacity, Cursor automatically compresses the conversation and resets the window with a summary. The agent receives a reference to the history file and can search it if details are missing from the summary.

### Caveats

- Summarization is lossy—the agent may lose track of working documents or reference materials
- After compression, you may need to re-attach key files with `@` references
- Quality degrades with repeated compressions in the same session

## 5. Surgical Context Control (@ Symbols)

The best context management strategy is avoiding overfill in the first place. Use `@` symbols to inject only what the AI needs.

| Symbol | Description | Available In |
|--------|-------------|--------------|
| `@Files` | Reference specific files | Chat, Cmd+K, Composer |
| `@Folders` | Reference entire directories (use sparingly) | Chat only |
| `@Code` | Reference specific functions, classes, or symbols | Chat, Cmd+K |
| `@Git` | Reference diffs or commit history | Chat only |
| `@Docs` | Reference library/framework documentation | Chat, Cmd+K |
| `@Web` | Perform live web search | Chat |
| `@Recent Changes` | Include recent file modifications | Chat |
| `@Past Chats` | Reference previous conversations | Chat |
| `@Lint Errors` | Include current lint/diagnostic errors | Chat only |
| `@Definitions` | Include symbol definitions | Cmd+K only |

### Tips

- Prefer `@Code` over `@Files` when you only need one function
- Avoid `@Folders` on large directories—pick 2-3 key files instead
- Use `@Git` (Diff of Working State) to focus on recent changes
- Drag and drop files from the sidebar into Chat or Cmd+K as an alternative

### Keyboard Shortcuts for Adding Context

| Shortcut | Action |
|----------|--------|
| `Cmd+Shift+L` | Add selected code to Chat |
| `Cmd+Shift+K` | Add selected code to Cmd+K |

## 6. Modes

Cursor offers different modes optimized for specific tasks. Switch using the mode picker or `Cmd+.`

| Mode | Description | Command |
|------|-------------|---------|
| **Agent** | Autonomous mode—explores codebase, edits files, runs commands | Default |
| **Ask** | Read-only mode for questions and exploration | `/ask` or `--mode=ask` |
| **Plan** | Planning mode with clarifying questions | `/plan` or `--mode=plan` |

In Ask mode, the AI cannot modify code or run commands—only provides answers.

## 7. Rules Configuration

Rules customize AI behavior for your project.

### Modern Approach (Recommended)

Create rules in `.cursor/rules/` as `.mdc` files:

```
.cursor/
  rules/
    general.mdc
    typescript.mdc
    testing.mdc
```

Rules can include:
- File pattern matching (gitignore-style)
- Automatic attachment when matching files are referenced
- `@file` references to include additional context

Create rules via Command Palette: `Cmd+Shift+P` > "New Cursor Rule"

### Legacy Approach

A `.cursorrules` file in the project root still works but is deprecated. If it gets bloated, split into `.cursor/rules/*.mdc` files to reduce token usage.

### Global Rules

Configure in `Cursor Settings > General > Rules for AI` for rules that apply across all projects.

## 8. Cursor CLI Quick Reference

| Action | Command |
|--------|---------|
| Install CLI | `curl https://cursor.com/install -fsSL \| bash` |
| Start interactive session | `cursor-agent` |
| Run with prompt (non-interactive) | `cursor-agent -p "your prompt"` |
| List all sessions | `agent ls` |
| Resume latest session | `agent resume` |
| Resume specific session | `cursor-agent --resume="<id>"` |
| Switch to Plan mode | `/plan` or `--mode=plan` |
| Switch to Ask mode | `/ask` or `--mode=ask` |
| Compress context | `/compress` |
| Rotate between modes | `Shift+Tab` |
| Review changes | `Ctrl+R` |
| JSON output (for scripts) | `--output-format json` |
| Send to cloud agent | Prepend `&` to message |

## 9. Editor Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Cmd+L` | Open Chat |
| `Cmd+I` | Open Composer |
| `Cmd+K` | Inline AI edit |
| `Cmd+N` | New chat thread |
| `Cmd+T` | New chat tab |
| `Cmd+.` | Switch modes |
| `Cmd+Shift+L` | Add selection to Chat |
| `Cmd+Shift+K` | Add selection to Cmd+K |
| `Tab` | Accept autocomplete |
| `Esc` | Reject AI suggestion |

Note: Use `Ctrl` instead of `Cmd` on Windows/Linux.

## Notes

- Context windows vary by model (e.g., 128k tokens ≈ 96k words ≈ 300-page book)
- Conversation history is stored locally, not synced across machines
- The CLI is still in beta—it can read, modify, delete files and execute commands
- Use interactive mode (not `-p`) when you need to approve commands
- Third-party extensions like "Cursor Usage & Cost Tracker" provide additional monitoring

## Sources

- [Cursor Features](https://cursor.com/features)
- [Cursor CLI Documentation](https://cursor.com/docs/cli/using)
- [Cursor @ Symbols Overview](https://docs.cursor.com/context/@-symbols/overview)
- [Cursor Rules Documentation](https://cursor.com/docs/context/rules)
- [Cursor Chat History](https://cursor.com/docs/agent/chat/history)
- [Cursor Modes](https://cursor.com/docs/agent/modes)
- [Cursor Summarization](https://cursor.com/docs/agent/chat/summarization)
