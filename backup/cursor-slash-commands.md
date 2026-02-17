# Cursor Slash Commands Reference

> Complete reference for Cursor CLI slash commands with detailed explanations.

**Source:** [Cursor Docs — Slash commands](https://cursor.com/docs/cli/reference/slash-commands)

## What Are Slash Commands?

Slash commands are special commands you type in the Cursor chat input (prefixed with `/`). They control how the AI behaves, manage your sessions, and configure your environment. Unlike regular prompts that ask the AI to do something, slash commands change settings or trigger system actions.

---

## Modes

Cursor has different modes that change how the AI interacts with your code.

| Command | Description |
|---------|-------------|
| `/plan` | Switch to **Plan mode**. The AI will help you design an approach before writing code. Use this when starting a complex feature—it forces you to think through the architecture first. The AI asks clarifying questions and outlines steps without making changes. |
| `/ask` | Switch to **Ask mode**. Read-only exploration—the AI can search, read files, and answer questions but cannot edit code or run commands. Use this when you want to understand a codebase without risk of accidental changes. |

**Default mode** is Agent mode, which has full access to edit files and run commands.

---

## Model & Behavior

Control which AI model runs and how it executes commands.

| Command | Description |
|---------|-------------|
| `/model <model>` | Switch the active AI model. Run `/model` alone to see available options (e.g., `claude-sonnet`, `gpt-4`, `claude-opus`). Different models have different strengths—Opus is more capable but slower and more expensive; Sonnet is faster for routine tasks. |
| `/max-mode [on\|off]` | Toggle extended context mode for models that support it. When on, the model can process more code at once (larger context window) but uses more tokens. Use for large refactors or when the AI keeps "forgetting" context. |
| `/auto-run [state]` | Control whether the AI automatically executes shell commands. `on` = commands run without asking (faster but riskier). `off` = always prompts for approval. `status` = show current setting. Default is on. Turn off when working with destructive commands or unfamiliar codebases. |

---

## Environment & Sandbox

Configure security and isolation settings.

| Command | Description |
|---------|-------------|
| `/sandbox` | Configure sandbox mode and network access. Sandboxing isolates the AI's actions to prevent unintended system changes. Use this to restrict what files/directories the AI can access, or to block network requests. Important for working on sensitive projects or when you don't fully trust an operation. |

---

## Chat & Sessions

Manage conversation history and context.

| Command | Description |
|---------|-------------|
| `/new-chat` | Start a fresh chat session with zero context. Use when switching to an unrelated task, or when the current conversation has become confused or polluted with wrong assumptions. Your old chat is saved and can be resumed later. |
| `/resume <chat>` | Resume a previous chat by its folder name. Run without arguments to see available chats. Useful for picking up where you left off on a long-running task, or for "forking" from a good starting point to try different approaches. |
| `/compress` | Summarize the current conversation to free up context space. When your chat gets long, the AI starts "forgetting" earlier messages. Compress condenses everything into a summary, giving you a fresh context window while preserving key decisions. Use when you see degraded responses or hit context limits. |

---

## Editor & Terminal

Configure your editing environment.

| Command | Description |
|---------|-------------|
| `/vim` | Toggle Vim keybindings on/off. If you're a Vim user, this enables familiar navigation (`hjkl`, modal editing, etc.) in the Cursor interface. If you accidentally enable it and get stuck, type `/vim` again to disable. |
| `/setup-terminal` | Auto-configure terminal keybindings for your shell. Fixes common issues where keyboard shortcuts conflict between Cursor and your terminal. Run this if copy/paste or other shortcuts aren't working as expected. See [Terminal Setup docs](https://cursor.com/docs/cli/reference/terminal-setup). |

---

## Help & Feedback

Get assistance and report issues.

| Command | Description |
|---------|-------------|
| `/help [command]` | Show help information. Run `/help` alone for general help, or `/help <command>` for details on a specific command (e.g., `/help model`). Start here when you're unsure what a command does. |
| `/feedback <message>` | Send feedback directly to the Cursor team. Use for bug reports, feature requests, or general comments. Example: `/feedback The auto-run feature saved me hours today`. |

---

## Usage & Account

Monitor usage and manage your account.

| Command | Description |
|---------|-------------|
| `/usage` | View your Cursor usage stats and streaks. Shows how many requests you've made, token consumption, and any usage limits. Check this if you're on a limited plan and want to monitor consumption. |
| `/about` | Display environment and CLI setup details. Shows your Cursor version, active model, configuration paths, and system info. Useful for debugging or when reporting issues. |
| `/copy-request-id` | Copy the last API request ID to your clipboard. Use this when reporting bugs—the request ID helps the Cursor team trace exactly what happened. |
| `/copy-conversation-id` | Copy the current conversation's unique ID. Similar to request ID, but identifies the entire chat session. Useful for bug reports or referencing specific conversations. |
| `/logout` | Sign out of your Cursor account. You'll need to sign in again to use paid features. Use when switching accounts or on shared machines. |
| `/quit` | Exit the Cursor CLI entirely. Same as Ctrl+C or closing the terminal. |

---

## MCP (Model Context Protocol)

MCP lets Cursor connect to external tools and data sources (databases, APIs, documentation servers). Think of MCP servers as plugins that extend what the AI can access.

| Command | Description |
|---------|-------------|
| `/mcp list` | Browse all available MCP servers. Shows which are enabled/disabled and lets you configure them. Use this to discover what integrations are available (e.g., database access, Jira, GitHub). |
| `/mcp enable <name>` | Enable an MCP server by name. Example: `/mcp enable github` to let the AI interact with GitHub. The server must be configured in your `mcp.json` first. |
| `/mcp disable <name>` | Disable an MCP server. Use when you want to temporarily cut off access to an external service, or if a server is causing issues. |

---

## Context: Rules & Commands

Manage project-specific AI behavior.

| Command | Description |
|---------|-------------|
| `/rules` | Create or edit Cursor rules. Rules are persistent instructions that shape AI behavior (e.g., "always use TypeScript", "never modify files in /legacy"). Opens your `.cursor/rules/` directory. Rules are auto-applied based on file patterns. |
| `/commands` | Create or edit custom slash commands. Commands are reusable prompts saved as markdown files in `.cursor/commands/`. Example: create a `/review` command that always runs your code review checklist. After creating, invoke with `/your-command-name`. |

---

## Quick Reference

| Command | One-liner |
|---------|-----------|
| `/plan` | Design before coding |
| `/ask` | Read-only exploration |
| `/model` | Switch AI model |
| `/max-mode` | Extended context window |
| `/auto-run` | Auto-execute commands |
| `/sandbox` | Security isolation |
| `/new-chat` | Fresh session |
| `/resume` | Continue previous chat |
| `/compress` | Summarize to free context |
| `/vim` | Toggle Vim keys |
| `/setup-terminal` | Fix terminal shortcuts |
| `/help` | Get help |
| `/feedback` | Report to Cursor team |
| `/usage` | View stats |
| `/about` | Environment info |
| `/copy-request-id` | Copy for bug reports |
| `/copy-conversation-id` | Copy session ID |
| `/logout` | Sign out |
| `/quit` | Exit CLI |
| `/mcp list` | Browse integrations |
| `/mcp enable` | Turn on integration |
| `/mcp disable` | Turn off integration |
| `/rules` | Edit AI rules |
| `/commands` | Edit custom commands |

## Syntax Notes

- `<value>` — Required argument. Example: `/model claude-sonnet`
- `[value]` — Optional argument. Example: `/auto-run` or `/auto-run off`
- `[a\|b\|c]` — Pick one option. Example: `/auto-run on` or `/auto-run off`
