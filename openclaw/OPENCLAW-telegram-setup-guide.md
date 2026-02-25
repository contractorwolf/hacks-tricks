# Telegram Setup Guide

Complete guide for connecting OpenClaw to Telegram via the Bot API.

## Overview

This guide covers:
- Creating a Telegram bot via BotFather
- OpenClaw configuration for Telegram
- Bot capabilities and features
- Security considerations

---

## Part 1: Create Your Telegram Bot

### 1.1 Start a Chat with BotFather

1. Open Telegram and search for `@BotFather`
2. Start a conversation by clicking **Start**
3. Send the command `/newbot`

### 1.2 Configure Your Bot

BotFather will guide you through setup:

1. **Choose a name**: This is the display name (e.g., "Penny Webb")
2. **Choose a username**: Must end in `bot` (e.g., `pennywebb_bot`)

BotFather will respond with:
```
Done! Congratulations on your new bot. You will find it at t.me/pennywebb_bot

Use this token to access the HTTP API:
1234567890:ABCdefGHIjklMNOpqrsTUVwxyz-1234567

Keep your token secure and store it safely, it can be used by anyone to control your bot.
```

⚠️ **Critical**: Save this token — you'll need it for OpenClaw configuration.

### 1.3 Optional: Customize Your Bot

Send these commands to BotFather to customize:

- `/setdescription` — Short description shown in chat
- `/setabouttext` — Longer description shown in profile
- `/setuserpic` — Upload a profile picture
- `/setcommands` — Define command menu (optional, OpenClaw handles commands internally)

---

## Part 2: OpenClaw Configuration

### 2.1 Add Telegram to Your Config

Edit your OpenClaw gateway config (or use `gateway config.patch`):

```json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "botToken": "1234567890:ABCdefGHIjklMNOpqrsTUVwxyz-1234567",
      "polling": true,
      "allowedChatIds": []
    }
  }
}
```

### 2.2 Configuration Options

| Key | Type | Description |
|-----|------|-------------|
| `enabled` | boolean | Enable/disable the Telegram channel |
| `botToken` | string | Bot token from BotFather (required) |
| `polling` | boolean | Use long-polling (recommended: `true`) |
| `allowedChatIds` | array | Restrict bot to specific chat/user IDs (empty = allow all) |

### 2.3 Security: Restrict Access

By default, anyone can message your bot. To restrict access:

1. **Find your Chat ID**:
   - Message your bot
   - Check OpenClaw logs for the chat ID (will appear in message metadata)
   
2. **Update config** with allowed IDs:
   ```json
   "allowedChatIds": [123456789, 987654321]
   ```

3. **Restart OpenClaw**:
   ```bash
   openclaw gateway restart
   ```

---

## Part 3: Using Your Telegram Bot

### 3.1 Start a Conversation

1. Open Telegram and search for your bot's username (e.g., `@pennywebb_bot`)
2. Click **Start** or send any message
3. The bot should respond (if OpenClaw is running)

### 3.2 Supported Features

**Text Messages**
- Full markdown formatting support
- Inline code and code blocks
- Links, bold, italic

**Media**
- Send/receive photos
- Send/receive documents
- Voice messages (if TTS configured)
- Stickers

**Interactions**
- Inline buttons (if configured in agent capabilities)
- Reply/quote messages
- Edit messages
- Reactions (via message tool)

**Commands**
OpenClaw handles these internally:
- `/status` — Session status
- `/help` — Help text (if configured)
- Custom commands via agent logic

### 3.3 Message Tool Actions

The `message` tool supports Telegram-specific actions:

**Send messages**:
```javascript
message(action: "send", target: "telegram_chat_id", message: "text")
```

**Send with effects** (Telegram premium features):
```javascript
message(action: "send", target: "chat_id", message: "text", effect: "🔥")
```

**Polls**:
```javascript
message(action: "poll-create", 
  target: "chat_id",
  pollQuestion: "Your question?",
  pollOption: ["Option 1", "Option 2", "Option 3"],
  pollMulti: false,
  pollDurationHours: 24
)
```

**Reactions**:
```javascript
message(action: "react", 
  target: "chat_id", 
  messageId: "message_id",
  emoji: "👍"
)
```

---

## Part 4: Troubleshooting

### Bot Not Responding

1. **Check OpenClaw is running**:
   ```bash
   openclaw gateway status
   ```

2. **Check logs**:
   ```bash
   tail -f ~/.openclaw/logs/gateway.log
   ```

3. **Verify token**: Ensure `botToken` in config is correct

4. **Test API token manually**:
   ```bash
   curl https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getMe
   ```

### Connection Issues

- **Polling vs Webhook**: OpenClaw uses polling by default (recommended for most setups)
- **Network/Firewall**: Ensure outbound HTTPS (443) is allowed
- **Rate Limits**: Telegram has rate limits; avoid spam

### Chat Not Allowed

If you see "Chat not allowed" errors:
- Check `allowedChatIds` in your config
- Verify the chat ID matches exactly
- Empty array `[]` = allow all chats

---

## Part 5: Advanced Configuration

### Multi-Bot Setup

You can run multiple Telegram bots from one OpenClaw instance:

```json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "botToken": "bot1_token",
      "allowedChatIds": [123456]
    },
    "telegram2": {
      "provider": "telegram",
      "enabled": true,
      "botToken": "bot2_token",
      "allowedChatIds": [789012]
    }
  }
}
```

### Group Chats

To add your bot to a group:

1. Add the bot to the group via Telegram
2. Send a message mentioning the bot
3. Check logs for the group chat ID
4. Add group ID to `allowedChatIds` (if using allowlist)

**Privacy Mode**:
- By default, bots in groups only see messages that mention them
- Disable via BotFather `/setprivacy` to see all messages

---

## Part 6: Best Practices

### Security

✅ **Do**:
- Use `allowedChatIds` to restrict access
- Keep your bot token secret (never commit to git)
- Regenerate token if compromised (BotFather `/revoke`)

❌ **Don't**:
- Share your bot token publicly
- Allow unrestricted access for production bots
- Commit config files with tokens to version control

### Performance

- Polling is reliable for most use cases
- Webhooks require public HTTPS endpoint (advanced setup)
- Monitor rate limits if sending bulk messages

### User Experience

- Set a clear description and profile picture
- Use markdown formatting for readable responses
- Leverage inline buttons for interactive workflows
- Handle /start gracefully (first message users send)

---

## Quick Reference

**Create bot**: `/newbot` via @BotFather  
**Get token**: BotFather provides after creation  
**Config location**: `~/.openclaw/config.json` or via `gateway config.patch`  
**Restart gateway**: `openclaw gateway restart`  
**Check status**: `openclaw gateway status`  
**View logs**: `tail -f ~/.openclaw/logs/gateway.log`

---

## Resources

- [Telegram Bot API Docs](https://core.telegram.org/bots/api)
- [BotFather Commands](https://core.telegram.org/bots/features#botfather)
- [OpenClaw Messaging Tool](https://docs.openclaw.ai/tools/message)
- [Gateway Configuration](https://docs.openclaw.ai/config/gateway)

---

**Need help?** Check [OpenClaw Discord](https://discord.com/invite/clawd) or [GitHub Issues](https://github.com/openclaw/openclaw).