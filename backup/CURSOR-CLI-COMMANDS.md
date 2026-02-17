# Cursor CLI & Slash Commands Reference

A detailed reference for the Cursor command-line interface (CLI) and in-chat slash commands.

---

## Part 1: Cursor CLI (Terminal Commands)

The Cursor CLI is invoked as `cursor` from your terminal. It opens or controls the Cursor editor and provides subcommands for tunneling, web serving, and running the Cursor Agent.

### Main Command

```bash
cursor [options] [paths...]
```

**Version:** Run `cursor -v` or `cursor --version` to see your installed version (e.g., Cursor 2.4.27).

To read from stdin, append `-` (e.g., `ps aux | grep code | cursor -`).

---

### General Options

| Option | Description |
|--------|-------------|
| `-h`, `--help` | Print usage. |
| `-v`, `--version` | Print version. |
| `-n`, `--new-window` | Force opening a new window. |
| `-r`, `--reuse-window` | Open the given file or folder in the last active window instead of a new one. |
| `-w`, `--wait` | Wait for the opened files to be closed before the CLI exits. |
| `--suppress-popups-on-startup` | Suppress notification popups on startup. |
| `--verbose` | Verbose output (implies `--wait`). |
| `--locale <locale>` | Locale (e.g. `en-US`, `zh-TW`). |
| `--user-data-dir <dir>` | Directory for user data. Use for multiple Cursor instances. |
| `--profile <profileName>` | Open with the given profile; creates it if it doesn’t exist. |

---

### File & Folder Options

| Option | Short | Arguments | Description |
|--------|-------|------------|-------------|
| `--diff` | `-d` | `<file> <file>` | Open diff view comparing two files. |
| `--merge` | `-m` | `<path1> <path2> <base> <result>` | Three-way merge: two modified versions, common base, and output file. |
| `--add` | `-a` | `<folder>` | Add folder(s) to the last active window. |
| `--remove` | — | `<folder>` | Remove folder(s) from the last active window. |
| `--goto` | `-g` | `<file:line[:character]>` | Open file at line (and optional character). |

**Examples:**

```bash
cursor -d file1.txt file2.txt
cursor -g src/main.ts:42:10
cursor -a ./packages/api
cursor -m theirs.txt ours.txt base.txt result.txt
```

---

### MCP (Model Context Protocol)

| Option | Arguments | Description |
|--------|------------|-------------|
| `--add-mcp` | `<json>` | Add an MCP server to user profile (or workspace when used with `--mcp-workspace`). JSON form: `{"name":"server-name","command":...}`. |

---

### Extensions Management

| Option | Arguments | Description |
|--------|------------|-------------|
| `--extensions-dir <dir>` | Path | Root path for extensions. |
| `--list-extensions` | — | List installed extensions. |
| `--show-versions` | — | Show versions when using `--list-extensions`. |
| `--category <category>` | Category | Filter by category with `--list-extensions`. |
| `--install-extension <ext-id \| path>` | ID or VSIX path | Install/update extension (e.g. `publisher.name` or `publisher.name@1.2.3`). Use `--force` to update. |
| `--pre-release` | — | Install pre-release when using `--install-extension`. |
| `--uninstall-extension <ext-id>` | ID | Uninstall extension. |
| `--update-extensions` | — | Update installed extensions. |
| `--enable-proposed-api <ext-id>` | One or more IDs | Enable proposed API for extension(s). |

---

### Troubleshooting Options

| Option | Short | Arguments | Description |
|--------|-------|------------|-------------|
| `--log <level>` | — | `critical`, `error`, `warn`, `info`, `debug`, `trace`, `off` | Log level. Can append `:${logLevel}` for extension (e.g. `publisher.name:trace`). |
| `--status` | `-s` | — | Print process usage and diagnostics. |
| `--prof-startup` | — | — | Run CPU profiler on startup. |
| `--disable-extensions` | — | — | Disable all extensions for this launch (not persisted). |
| `--disable-extension <ext-id>` | — | ID | Disable one extension for this launch (not persisted). |
| `--sync <on \| off>` | — | — | Turn sync on or off. |
| `--inspect-extensions <port>` | — | Port | Debug/profile extensions; use devtools for connection URI. |
| `--inspect-brk-extensions <port>` | — | Port | Same as above but pause extension host at start. |
| `--disable-lcd-text` | — | — | Disable LCD font rendering. |
| `--disable-gpu` | — | — | Disable GPU acceleration. |
| `--disable-chromium-sandbox` | — | — | Disable Chromium sandbox (e.g. when running as root). |
| `--locate-shell-integration-path <shell>` | — | `bash`, `pwsh`, `zsh`, `fish` | Print path to shell integration script. |
| `--telemetry` | — | — | Show telemetry events. |

---

### Subcommand: `tunnel`

Make your machine accessible from vscode.dev or other machines via a secure tunnel.

```bash
cursor tunnel [OPTIONS] [COMMAND]
```

**Options:**

| Option | Description |
|--------|-------------|
| `--install-extension <id>` | Preload/install extension on connecting servers. |
| `--server-data-dir <dir>` | Server data directory. |
| `--extensions-dir <dir>` | Extensions root path. |
| `-h`, `--help` | Help. |

**Advanced:**

| Option | Description |
|--------|-------------|
| `--random-name` | Random machine name for port forwarding. |
| `--no-sleep` | Prevent sleep while tunnel runs. |
| `--name <NAME>` | Machine name for port forwarding. |
| `--accept-server-license-terms` | Accept server license without prompt. |

**Global (tunnel):** `--cli-data-dir`, `--verbose`, `--log <level>`.

**Commands:**

| Command | Description |
|---------|-------------|
| `prune` | Delete all servers that are not currently running. |
| `kill` | Stop any running tunnel. |
| `restart` | Restart any running tunnel. |
| `status` | Check if a tunnel is running. |
| `rename` | Rename this machine for the port forwarding service. |
| `unregister` | Remove this machine from the port forwarding service. |
| `user` | Log in/out and show account (see below). |
| `service` | Manage tunnel as system service (see below). |
| `help` | Help for tunnel. |

**Tunnel user:**

```bash
cursor tunnel user login    # Log in to port forwarding
cursor tunnel user logout   # Log out
cursor tunnel user show     # Show current account
```

**Tunnel service (preview):**

```bash
cursor tunnel service install    # Install tunnel as system service
cursor tunnel service uninstall  # Uninstall and stop service
cursor tunnel service log        # Show service logs
```

---

### Subcommand: `serve-web`

Run a local web version of Cursor in the browser.

```bash
cursor serve-web [OPTIONS]
```

| Option | Description |
|--------|-------------|
| `--host <HOST>` | Host to bind (default: `localhost`). |
| `--port <PORT>` | Port (default: `8000`; use `0` for random). |
| `--socket-path <PATH>` | Unix socket path (alternative to host/port). |
| `--connection-token <TOKEN>` | Secret required in requests. |
| `--connection-token-file <FILE>` | File containing the connection token. |
| `--without-connection-token` | Run without token (only if secured elsewhere). |
| `--accept-server-license-terms` | Accept license without prompt. |
| `--server-base-path <PATH>` | Base path for web UI and code server. |
| `--server-data-dir <DIR>` | Server data directory. |
| `-h`, `--help` | Help. |

**Global:** `--cli-data-dir`, `--verbose`, `--log <level>`.

---

### Subcommand: `agent`

Start the Cursor Agent in the terminal (and manage auth, MCP, rules, etc.).

```bash
cursor agent [options] [command] [prompt...]
```

**Arguments:**

- `prompt` — Optional initial prompt for the agent.

**Options:**

| Option | Short | Arguments | Description |
|--------|-------|------------|-------------|
| `--help` | `-h` | — | Help. |
| `--version` | `-v` | — | Version. |
| `--api-key <key>` | — | Key | API key (or `CURSOR_API_KEY` env). |
| `--header <header>` | `-H` | `Name: Value` (repeatable) | Custom header for agent requests. |
| `--print` | `-p` | — | Print responses to console (scriptable); has access to tools including write and bash. Default: `false`. |
| `--output-format <format>` | — | `text`, `json`, `stream-json` | With `--print` only. Default: `text`. |
| `--stream-partial-output` | — | — | With `--print` and `stream-json`: stream partial output. Default: `false`. |
| `--cloud` | `-c` | — | Start in cloud mode (composer picker). Default: `false`. |
| `--mode <mode>` | — | `plan`, `ask` | `plan`: read-only/planning; `ask`: Q&A, read-only. |
| `--plan` | — | — | Shorthand for `--mode=plan`. Ignored if `--cloud` is set. |
| `--resume [chatId]` | — | Optional ID | Resume a chat. |
| `--continue` | — | — | Resume the latest chat. |
| `--model <model>` | — | e.g. `gpt-5`, `sonnet-4`, `sonnet-4-thinking` | Model to use. |
| `--list-models` | — | — | List available models and exit. |
| `--force` | `-f` | — | Allow commands unless explicitly denied. Default: `false`. |
| `--sandbox <mode>` | — | `enabled`, `disabled` | Override sandbox. |
| `--approve-mcps` | — | — | Auto-approve MCP servers (only with `--print`/headless). Default: `false`. |
| `--workspace <path>` | — | Path | Workspace directory (default: current directory). |

**Agent subcommands:**

| Command | Description |
|---------|-------------|
| `agent [prompt...]` | Start the Cursor Agent (default command). |
| `install-shell-integration` | Install shell integration into `~/.zshrc`. |
| `uninstall-shell-integration` | Remove shell integration from `~/.zshrc`. |
| `login` | Authenticate with Cursor (set `NO_OPEN_BROWSER` to skip opening browser). |
| `logout` | Sign out and clear stored auth. |
| `mcp` | Manage MCP servers (see below). |
| `status`, `whoami` | Show authentication status. |
| `models` | List models available for your account. |
| `about` | Version, system, and account info. |
| `update`, `upgrade` | Update Cursor Agent. |
| `create-chat` | Create an empty chat and return its ID. |
| `generate-rule`, `rule` | Generate a Cursor rule interactively. |
| `ls` | Resume a chat session. |
| `resume` | Resume the latest chat. |
| `help [command]` | Help for agent or a subcommand. |

**Agent MCP:**

```bash
cursor agent mcp list                    # List configured MCPs and status
cursor agent mcp list-tools <identifier> # List tools and args for one MCP
cursor agent mcp login <identifier>      # Auth for MCP in .cursor/mcp.json or ~/.cursor/mcp.json
cursor agent mcp enable <identifier>    # Add MCP to approved list
cursor agent mcp disable <identifier>   # Disable MCP (won’t load or prompt)
```

**Examples:**

```bash
cursor agent
cursor agent "Refactor this function for clarity"
cursor agent --plan
cursor agent --print --output-format json "List files in ."
cursor agent login
cursor agent models
cursor agent mcp list
```

---

## Part 2: In-Chat Slash Commands

In Cursor’s chat, you can trigger **custom commands** by typing `/` in the input box. These are not part of the terminal CLI; they’re defined as Markdown files and appear when you type `/`.

### How they work

- Commands are **Markdown files** (`.md`).
- They are loaded from:
  1. **Project:** `.cursor/commands/` in the project root  
  2. **Global:** `~/.cursor/commands/` in your home directory  
  3. **Team:** Created by team admins in the [Team Content dashboard](https://cursor.com/dashboard?tab=team-content&section=commands) (Team/Enterprise); synced to all members  

When you type `/`, Cursor shows all available commands from these locations.

### Creating commands

1. Create a directory: `.cursor/commands/` in your project (or use `~/.cursor/commands/` for global).
2. Add `.md` files with clear names (e.g. `review-code.md`, `write-tests.md`).
3. Write Markdown that describes what the command should do (the prompt the model sees).
4. The command name in the list is derived from the filename (e.g. `review-code` → `/review-code`).

**Example layout:**

```
.cursor/
└── commands/
    ├── review-code.md
    ├── write-tests.md
    ├── create-pr.md
    └── security-audit.md
```

### Parameters / extra context

Anything you type **after** the command name is passed to the model as additional context:

```text
/commit and /pr these changes to address DX-523
```

So the model sees both the command’s Markdown and “these changes to address DX-523”.

### Team commands (Team / Enterprise)

- Created in the [Team Content dashboard](https://cursor.com/dashboard?tab=team-content&section=commands) (Team Content → commands).
- You set: **Name** (what appears after `/`), optional **Description**, and **Content** (Markdown).
- Same behavior as project/global commands: type `/` to see and use them; no manual sync.

---

## Quick reference

| Context | Invocation | Purpose |
|--------|------------|---------|
| Terminal | `cursor [options] [paths...]` | Open Cursor, diff, merge, add folders, etc. |
| Terminal | `cursor tunnel ...` | Tunneling and port forwarding. |
| Terminal | `cursor serve-web ...` | Run Cursor in the browser. |
| Terminal | `cursor agent ...` | Run Cursor Agent and auth/MCP/rule commands. |
| Chat | `/command-name optional context` | Run a custom prompt from `.cursor/commands/` or team commands. |

For the latest CLI behavior, run `cursor --help` and `cursor agent --help` (and `cursor tunnel --help`, `cursor serve-web --help`) on your machine.

---

## References (official Cursor docs, verified)

- [Cursor documentation](https://cursor.com/docs)
- [Commands (in-chat slash commands)](https://cursor.com/docs/context/commands)
- [Team Content / team commands](https://cursor.com/dashboard?tab=team-content&section=commands)
- [Dashboard](https://cursor.com/dashboard)
