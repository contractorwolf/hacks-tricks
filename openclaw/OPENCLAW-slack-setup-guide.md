# Slack Bot Setup Guide for OpenClaw

*Documented: February 16, 2026 — based on real setup experience*

---

## Overview

Connecting a Slack bot to OpenClaw involves multiple moving parts that aren't always obvious. This guide documents the pitfalls, gotchas, and steps we discovered through trial and error.

## Key Slack Configuration Pages

All configuration happens at **[api.slack.com/apps](https://api.slack.com/apps)**.

| Page | URL Pattern | Purpose |
|------|------------|---------|
| **Your Apps** | `api.slack.com/apps` | List all your Slack apps |
| **Socket Mode** | `api.slack.com/apps/{APP_ID}/socket-mode` | Enable/disable Socket Mode |
| **OAuth & Permissions** | `api.slack.com/apps/{APP_ID}/oauth` | Bot/User tokens and scopes |
| **Event Subscriptions** | `api.slack.com/apps/{APP_ID}/event-subscriptions` | Which events the bot receives |
| **App Home** | `api.slack.com/apps/{APP_ID}/app-home` | DM/home tab settings |

## Tokens You Need

### Bot User OAuth Token (`xoxb-...`)
- Found on **OAuth & Permissions** page
- This is the primary token for sending/reading messages
- **Critical:** This token is minted with a specific set of scopes. Adding scopes later does NOT update the existing token — you must **reinstall the app** to get a new token with the updated scopes.

### App-Level Token (`xapp-...`)
- Found on **Basic Information** → **App-Level Tokens**
- Required for **Socket Mode** connections
- Must have the `connections:write` scope

## Bot Token Scopes — Complete Reference

All scopes are configured on the **OAuth & Permissions** page under **Bot Token Scopes**.

### Required (Core Functionality)

Without these, the bot won't function properly with OpenClaw.

| Scope | What It Does | Why You Need It |
|-------|-------------|-----------------|
| `app_mentions:read` | Lets the bot see when someone types `@BotName` in a channel | This is the primary trigger — without it, the bot is deaf in channels |
| `chat:write` | Lets the bot send messages to any channel it's a member of, and to DMs | The bot literally cannot respond without this |
| `channels:history` | Lets the bot read message history in **public** channels | Needed to see what people said (not just mentions — full context) |
| `channels:read` | Lets the bot see a list of public channels and their metadata | Used to resolve channel names to IDs, check channel info |
| `im:history` | Lets the bot read DM conversation history | Without this, the bot can't see what you said in a DM |
| `im:read` | Lets the bot see that a DM conversation exists | Needed to detect and list DM conversations |
| `im:write` | Lets the bot open new DM conversations | Required to initiate a DM (not just reply to one) |
| `users:read` | Lets the bot look up user profiles by ID | Needed to resolve user names, avatars, and display names |

### Recommended (Full-Featured Bot)

These enable richer interaction and are expected by most OpenClaw features.

| Scope | What It Does | Why You Want It |
|-------|-------------|-----------------|
| `reactions:read` | Lets the bot see emoji reactions on messages | Useful for reading acknowledgments, polls, sentiment |
| `reactions:write` | Lets the bot add emoji reactions to messages | The bot can react with ✅, 👀, etc. to acknowledge messages without cluttering the chat |
| `files:write` | Lets the bot upload and share files | Required to send file attachments (images, documents, code files) |
| `files:read` | Lets the bot access file metadata and content | Needed if the bot should read/download files others share |
| `pins:read` | Lets the bot see pinned messages in a channel | Useful for reading pinned context, meeting notes, etc. |
| `pins:write` | Lets the bot pin and unpin messages | Can pin important bot responses or summaries |
| `emoji:read` | Lets the bot list custom workspace emoji | Needed if bot should use custom emoji or list available reactions |

### Optional (Private Channels & Groups)

Only needed if you want the bot to work in private channels.

| Scope | What It Does | Why You Might Need It |
|-------|-------------|----------------------|
| `groups:history` | Read messages in private channels the bot is invited to | Same as `channels:history` but for private channels |
| `groups:read` | See private channel info and membership | Same as `channels:read` but for private channels |
| `groups:write` | Manage private channels (invite users, set topic, etc.) | Only if you want the bot to manage private channel settings |
| `mpim:history` | Read messages in multi-person DMs | Needed for group DMs (not channels — direct multi-party chats) |
| `mpim:read` | See multi-person DM metadata | Detect and list group DM conversations |
| `mpim:write` | Create multi-person DMs | Initiate group DMs from the bot |

### Advanced / Specialized

These are situational — add them only if you need the specific capability.

| Scope | What It Does | Use Case |
|-------|-------------|----------|
| `users:read.email` | Access user email addresses | If bot needs to match Slack users to email-based systems |
| `users.profile:read` | Read detailed user profile fields | Custom profile fields, status, pronouns |
| `team:read` | Read workspace metadata | Get workspace name, domain, icon |
| `usergroups:read` | Read user groups (e.g., @engineering) | Resolve user group mentions to member lists |
| `commands` | Add and handle slash commands | If you register `/commands` for the bot |
| `chat:write.customize` | Send messages with a custom username and avatar | Post as different personas (e.g., "Alert Bot" vs "Helper Bot") |
| `chat:write.public` | Post to channels the bot hasn't joined | Send to any public channel without needing `/invite` first |
| `incoming-webhook` | Post to a single channel via webhook URL | Legacy — Socket Mode doesn't need this |

### Event Subscriptions (Separate from Scopes)

On the **Event Subscriptions** page, you also need to subscribe to **bot events**. These control what the bot *hears*, while scopes control what it *can do*.

| Event | What It Triggers | Required? |
|-------|-----------------|-----------|
| `message.im` | Any message in a DM with the bot | ✅ Yes — DMs won't work without it |
| `message.channels` | Any message in a public channel the bot is in | ✅ Yes — channel messages won't arrive |
| `app_mention` | Someone @mentions the bot | ✅ Yes — primary trigger in channels |
| `message.groups` | Messages in private channels | Optional — only for private channels |
| `message.mpim` | Messages in multi-person DMs | Optional — only for group DMs |
| `member_joined_channel` | Someone joins a channel | Optional — awareness of membership changes |
| `reaction_added` | Someone reacts to a message | Optional — if bot should respond to reactions |

## Setup Steps (In Order)

### 1. Create the Slack App
- Go to [api.slack.com/apps](https://api.slack.com/apps) → **Create New App**
- Choose **From scratch** (not from manifest)
- Name it and select your workspace

### 2. Enable Socket Mode
- Go to **Socket Mode** in the left sidebar
- Toggle it **ON**
- Create an app-level token with `connections:write` scope
- Copy the `xapp-...` token

> ⚠️ **Gotcha:** If Socket Mode isn't enabled, the bot will appear to connect but will log:
> `bolt-app Socket Mode is not turned on.`
> Events will NOT be delivered.

### 3. Add Bot Token Scopes
- Go to **OAuth & Permissions**
- Scroll to **Bot Token Scopes**
- Add all required scopes listed above

### 4. Install the App to Workspace
- On **OAuth & Permissions**, click **Install to Workspace** (or **Reinstall to Workspace**)
- Authorize the permissions
- Copy the **Bot User OAuth Token** (`xoxb-...`)

### 5. Subscribe to Events
- Go to **Event Subscriptions** → toggle **ON**
- Under **Subscribe to bot events**, add:
  - `message.im` (DM messages)
  - `message.channels` (public channel messages)
  - `app_mention` (when someone @mentions the bot)
  - `message.groups` (private channels, if needed)

### 6. Configure OpenClaw
```bash
openclaw setup slack
```
Enter your `xoxb-` bot token and `xapp-` app token when prompted.

### 7. Invite the Bot to Channels
In each Slack channel where you want the bot active:
```
/invite @YourBotName
```

> ⚠️ **Gotcha:** Without this, sending to a channel returns `not_in_channel`. The bot can only post in channels it's a member of.

## The #1 Gotcha: Token Scope Minting

**This is the single biggest issue we encountered.**

When you add or change scopes on the OAuth & Permissions page, the **existing bot token does NOT automatically gain those scopes**. The token was "minted" with whatever scopes existed at install time.

### You MUST:
1. Add the scopes
2. **Reinstall the app** to the workspace (yellow banner appears at top of OAuth page)
3. Copy the **new** bot token (it may or may not change in value)
4. Update the token in OpenClaw (`openclaw setup slack`)
5. Restart the gateway

### How to tell it's working:
- `openclaw status --deep` shows Slack as `ON / OK`
- Gateway logs show `[slack] socket mode connected` with **no** `Socket Mode is not turned on` warning
- Sending a message returns success, not `missing_scope`

## Debugging Commands

```bash
# Check overall status
openclaw status --deep

# Watch live logs for Slack events
openclaw logs --follow

# Check gateway logs for errors
journalctl --user -u openclaw-gateway --no-pager -n 100 | grep -i slack

# Restart gateway after config changes
systemctl --user restart openclaw-gateway
```

## Common Errors and Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| `missing_scope` | Bot token doesn't have the required scope | Add scope → Reinstall app → Update token |
| `not_in_channel` | Bot isn't a member of the channel | `/invite @BotName` in the channel |
| `user_not_found` | Invalid user ID or lookup failed | Use Slack user ID (starts with `U`), not display name |
| `Socket Mode is not turned on` | Socket Mode disabled in app settings | Enable it on the Socket Mode page |
| `channel_not_found` | Wrong channel ID or name | Use the actual channel ID (starts with `C` or `D`) |

## OpenClaw Config Reference

The Slack channel config in `openclaw.json`:

```json
{
  "channels": {
    "slack": {
      "mode": "socket",
      "enabled": true,
      "botToken": "xoxb-...",
      "appToken": "xapp-...",
      "groupPolicy": "open",
      "dmPolicy": "open",
      "allowFrom": ["*"]
    }
  }
}
```

### Policy Options
- **`dmPolicy`**: `"open"` (anyone can DM) or `"allowlist"` (restricted)
- **`groupPolicy`**: `"open"` (responds in any channel) or `"allowlist"` (restricted)
- **`allowFrom`**: `["*"]` for everyone, or specific user/channel IDs

> ⚠️ **Security Note:** `groupPolicy: "open"` with elevated tools enabled is a security risk. Consider using `"allowlist"` in production to prevent prompt injection from untrusted channels.

## Timeline of Our Setup (for reference)

1. Initial config — Socket Mode not enabled on Slack side → bot connected but received no events
2. Enabled Socket Mode — events started flowing, but `missing_scope` on all sends
3. Added `chat:write` scope — but didn't reinstall → token still lacked the scope
4. Reinstalled app, updated token — `chat:write` worked (got `not_in_channel` instead)
5. Added remaining scopes (`channels:history`, `im:*`, `users:read`) — full functionality
6. Invited bot to channels — complete working setup

**Total time to debug: ~3 hours.** Most of that was the token re-minting gotcha.

---

*Pro tip: Get all your scopes right BEFORE the first install. It saves the reinstall dance.*
