# OpenClaw Channels Reference

> Messaging channel setup and configuration

## Supported Channels

| Channel | Protocol | Auth Method | Status |
|---------|----------|-------------|--------|
| WhatsApp | Web (Baileys) | QR linking | Production |
| Telegram | Bot API (grammY) | Bot token | Production |
| Discord | Bot (discord.js) | Bot token | Production |
| Slack | Bolt | Bot token | Production |
| Signal | signal-cli | Phone number | Production |
| iMessage | BlueBubbles | macOS server | Production |
| Google Chat | Chat API | Service account | Production |
| Microsoft Teams | Bot Framework | App registration | Plugin |
| Matrix | Client-Server API | Access token | Plugin |
| IRC | IRC protocol | Nick/password | Production |
| Feishu | Bot API | App credentials | Plugin |
| LINE | Messaging API | Channel token | Plugin |
| Mattermost | Bot API | Bot token | Plugin |
| Nextcloud Talk | Bot API | Bot token | Plugin |
| Nostr | Relay protocol | Private key | Plugin |
| Synology Chat | Webhook | Token | Plugin |
| Tlon | Urbit | Ship credentials | Plugin |
| Twitch | Chat/IRC | OAuth | Plugin |
| Zalo | OA API | App credentials | Plugin |
| WebChat | WebSocket | Gateway auth | Built-in |

---

## Common Channel Config

### DM Policy

```json5
{
  channels: {
    telegram: {
      enabled: true,
      dmPolicy: "pairing",   // pairing | allowlist | open | disabled
      allowFrom: ["tg:123"],
    },
  },
}
```

| Policy | Behavior |
|--------|----------|
| `pairing` | Unknown senders get pairing code (default) |
| `allowlist` | Only senders in `allowFrom` |
| `open` | All inbound DMs (requires `allowFrom: ["*"]`) |
| `disabled` | Ignore all DMs |

### Group Policy

```json5
{
  channels: {
    discord: {
      groupPolicy: "allowlist",
      groupAllowFrom: ["123456789012345678"],
      groups: {
        "*": { requireMention: true },
        "123456789012345678": { requireMention: false },
      },
    },
  },
}
```

---

## Telegram Setup

### Quick Config

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "123456:ABC-DEF",
      dmPolicy: "pairing",
      groups: { "*": { requireMention: true } },
    },
  },
}
```

### BotFather Commands

- `/newbot` - Create new bot
- `/setprivacy` - Toggle privacy mode
- `/setjoingroups` - Allow/deny group adds

### CLI

```bash
# Set token via env
TELEGRAM_BOT_TOKEN=123:abc openclaw gateway

# Pairing
openclaw pairing list telegram
openclaw pairing approve telegram <CODE>
```

---

## WhatsApp Setup

### Quick Config

```json5
{
  channels: {
    whatsapp: {
      dmPolicy: "pairing",
      allowFrom: ["+15551234567"],
      groupPolicy: "allowlist",
      groupAllowFrom: ["+15551234567"],
    },
  },
}
```

### CLI

```bash
# Link via QR
openclaw channels login --channel whatsapp

# Multi-account
openclaw channels login --channel whatsapp --account work

# Pairing
openclaw pairing list whatsapp
openclaw pairing approve whatsapp <CODE>
```

---

## Discord Setup

### Quick Config

```json5
{
  channels: {
    discord: {
      enabled: true,
      botToken: "OTk...",
      applicationId: "123456789012345678",
      dmPolicy: "pairing",
      groups: {
        "*": { requireMention: true },
      },
      guilds: {
        "123456789012345678": {
          enabled: true,
          channels: {
            "987654321098765432": { enabled: true },
          },
        },
      },
    },
  },
}
```

### CLI

```bash
# Login
openclaw channels login discord

# Status
openclaw channels status discord
```

---

## Slack Setup

### Quick Config

```json5
{
  channels: {
    slack: {
      enabled: true,
      botToken: "xoxb-...",
      appToken: "xapp-...",
      signingSecret: "...",
      dmPolicy: "pairing",
      groups: { "*": { requireMention: true } },
    },
  },
}
```

### CLI

```bash
openclaw channels login slack
```

---

## Signal Setup

### Quick Config

```json5
{
  channels: {
    signal: {
      enabled: true,
      phoneNumber: "+15551234567",
      dmPolicy: "pairing",
    },
  },
}
```

### CLI

```bash
# Register
openclaw channels login signal

# Verify
openclaw channels verify signal <CODE>
```

---

## Multi-Account

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "main-bot-token",
      accounts: {
        work: {
          botToken: "work-bot-token",
        },
      },
    },
  },
}
```

---

## Troubleshooting

```bash
# Channel status
openclaw channels status --probe

# Diagnostics
openclaw doctor

# Logs
openclaw logs --follow
```
