# hacks&tricks

Command-line tricks and workflow hacks for engineers. Each page is a self-contained reference with the code upfront and explanations that teach.

## Contents

### Git

- **[git-browse-alias.md](git/git-browse-alias.md)** — Open your repo's GitHub/GitLab page directly from the terminal. Creates a global `git browse` command that converts SSH remote URLs to HTTPS and opens them in your default browser.
- **[git-cherry-pick-workflow.md](git/git-cherry-pick-workflow.md)** — A workflow for promoting changes across multiple environment branches (`dev`, `stage`, `main`) using cherry-pick. Keeps history clean with one commit per environment and avoids merge commit clutter.

### Claude Code

- **[CLAUDE_CONTEXT_FORKING_GUIDE.md](claude-code/CLAUDE_CONTEXT_FORKING_GUIDE.md)** — Manage long Claude Code sessions by building a "seed" context then forking from it with `/resume`. Each fork gets full understanding with a fresh token budget.
- **[CLAUDE_SKILLS_GUIDE.md](claude-code/CLAUDE_SKILLS_GUIDE.md)** — Guide to creating and using custom skills (slash commands) in Claude Code. Covers skill file structure, placement, and examples.
- **[GITHUB_CLAUDE_GUIDE.md](claude-code/GITHUB_CLAUDE_GUIDE.md)** — Using Claude as an AI code reviewer and autonomous agent on GitHub. Covers `@claude` mentions in PRs/issues, repository setup, and the `claude.yml` config.

### Cursor

- **[cursor-slash-commands.md](cursor/cursor-slash-commands.md)** — Reference for Cursor CLI slash commands. Covers modes (`/plan`, `/ask`), model switching, session management (`/resume`, `/compress`), MCP server control, and account commands.
- **[CURSOR_BROWSER_USAGE.md](cursor/CURSOR_BROWSER_USAGE.md)** — Guide to Cursor's Browser Agent. Covers using `@browser` in Composer to navigate pages, read console errors, take screenshots, and run end-to-end flow tests against your local dev server.
- **[CURSOR_CONTEXT_GUIDE.md](cursor/CURSOR_CONTEXT_GUIDE.md)** — How Cursor builds and manages context. Covers `@` symbols, `.cursorrules`, docs indexing, and strategies for keeping the model focused.
- **[CURSOR-CLI-COMMANDS.md](cursor/CURSOR-CLI-COMMANDS.md)** — Full reference for Cursor's CLI commands and keyboard shortcuts across all modes (Composer, Chat, Terminal).
- **[BUGBOT_FOR_GITLAB.md](cursor/BUGBOT_FOR_GITLAB.md)** — AI code reviewer that scans GitLab Merge Requests for logic bugs, security issues, and edge cases. Setup and configuration for Cursor's Bugbot integration.

### OpenClaw

- **[OPENCLAW-MARKDOWN-ARCHITECTURE.md](openclaw/OPENCLAW-MARKDOWN-ARCHITECTURE.md)** — Comprehensive reference to every markdown file that defines an OpenClaw agent's personality, behavior, and capabilities. Covers file locations, loading order, behavioral techniques, and advanced patterns like persona switching and memory priming.
- **[BUILD-YOUR-ASSISTANT.md](openclaw/BUILD-YOUR-ASSISTANT.md)** — Step-by-step guide to designing, building, and testing a custom AI assistant using OpenClaw's markdown-driven architecture. Covers personality files, knowledge setup, skill configuration, and iterative testing.
- **[JOB-CHARACTER-STRATEGIES.md](openclaw/JOB-CHARACTER-STRATEGIES.md)** — Demonstrates how to build AI "employees" for common service roles — hotel concierge, IT help desk, and retail customer service — using OpenClaw's markdown architecture. Includes complete SOUL.md, AGENTS.md, and config examples for each role.
- **[OPENCLAW-slack-setup-guide.md](openclaw/OPENCLAW-slack-setup-guide.md)** — End-to-end guide for connecting a Slack bot to OpenClaw via Socket Mode. Covers token types, every bot scope with explanations, event subscriptions, and the critical token re-minting gotcha that costs most people hours of debugging.
- **[OPENCLAW-telegram-setup-guide.md](openclaw/OPENCLAW-telegram-setup-guide.md)** — End-to-end guide for connecting a Telegram bot to OpenClaw.





### General

- **[browser-refresh.md](general/browser-refresh.md)** — Reload your browser's active tab from the terminal using AppleScript. Tries Chrome, falls back to Safari, then prints a manual instruction. Useful for rapid iteration during frontend development.
- **[check-text-for-ai.md](general/check-text-for-ai.md)** — Forensic linguistic analysis prompt for detecting AI-generated text. Assesses whether content is human-written, AI-assisted, or primarily AI-generated.
