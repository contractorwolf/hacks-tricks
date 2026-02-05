# hacks&tricks

Command-line tricks and workflow hacks for engineers. Each page is a self-contained reference with the code upfront and explanations that teach.

## Contents

### Git

- **[git-browse-alias.md](git-browse-alias.md)** — Open your repo's GitHub/GitLab page directly from the terminal. Creates a global `git browse` command that converts SSH remote URLs to HTTPS and opens them in your default browser.
- **[git-cherry-pick-workflow.md](git-cherry-pick-workflow.md)** — A workflow for promoting changes across multiple environment branches (`dev`, `stage`, `main`) using cherry-pick. Keeps history clean with one commit per environment and avoids merge commit clutter.

### AI Editors & Assistants

- **[CLAUDE_CONTEXT_FORKING_GUIDE.md](CLAUDE_CONTEXT_FORKING_GUIDE.md)** — Manage long Claude Code sessions by building a "seed" context then forking from it with `/resume`. Each fork gets full understanding with a fresh token budget.
- **[cursor-slash-commands.md](cursor-slash-commands.md)** — Reference for Cursor CLI slash commands. Covers modes (`/plan`, `/ask`), model switching, session management (`/resume`, `/compress`), MCP server control, and account commands.
- **[CURSOR_BROWSER_USAGE.md](CURSOR_BROWSER_USAGE.md)** — Guide to Cursor's Browser Agent. Covers using `@browser` in Composer to navigate pages, read console errors, take screenshots, and run end-to-end flow tests against your local dev server.

### [BUILD-YOUR-ASSISTANT.md](BUILD-YOUR-ASSISTANT.md)
Step-by-step guide to designing, building, and testing a custom AI assistant using OpenClaw's markdown-driven architecture. Covers personality files, knowledge setup, skill configuration, and iterative testing.

### [cursor-slash-commands.md](cursor-slash-commands.md)
Reference for Cursor CLI slash commands. Covers modes (`/plan`, `/ask`), model switching, session management (`/resume`, `/compress`), MCP server control, and account commands.

### [JOB-CHARACTER-STRATEGIES.md](JOB-CHARACTER-STRATEGIES.md)
Demonstrates how to build AI "employees" for common service roles — hotel concierge, IT help desk, and retail customer service — using OpenClaw's markdown architecture. Includes complete SOUL.md, AGENTS.md, and config examples for each role.

### [OPENCLAW-MARKDOWN-ARCHITECTURE.md](OPENCLAW-MARKDOWN-ARCHITECTURE.md)
Comprehensive reference to every markdown file that defines an OpenClaw agent's personality, behavior, and capabilities. Covers file locations, loading order, behavioral techniques, and advanced patterns like persona switching and memory priming.

### Terminal & Shell

- **[browser-refresh.md](browser-refresh.md)** — Reload your browser's active tab from the terminal using AppleScript. Tries Chrome, falls back to Safari, then prints a manual instruction. Useful for rapid iteration during frontend development.
