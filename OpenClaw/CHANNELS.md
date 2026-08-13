# OpenClaw Channels Reference

> Messaging channel setup, streaming modes, and configuration — current as of 2026-08-13 (upstream `0926d56cbf9`).

## Supported Channels

| Channel | Transport | Auth Method | Status |
|---------|-----------|-------------|--------|
| WhatsApp | Web (Baileys) | QR linking | Production |
| Telegram | Bot API (grammY, long polling) | Bot token | Production |
| Discord | discord.js | Bot token | Production |
| Slack | Bolt | Bot token + app token | Production |
| Signal | signal-cli | Phone number | Production |
| iMessage | BlueBubbles | macOS server | Production |
| Google Chat | Chat API | Service account | Production |
| IRC | IRC protocol | Nick/password | Production |
| Microsoft Teams | Bot Framework | App registration | Plugin |
| Matrix | Client-Server API | Access token | Plugin |
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

## DM Policy (All Channels)

| Policy | Behavior |
|--------|----------|
| `pairing` | Unknown senders get a one-time pairing code (default) |
| `allowlist` | Only senders in `allowFrom` |
| `open` | All inbound DMs (requires `allowFrom: ["*"]`) |
| `disabled` | Ignore all DMs |

```json5
{
  channels: {
    telegram: {
      enabled: true,
      dmPolicy: "pairing",
      allowFrom: ["tg:123456789"],
    },
  },
}
```

## Group Policy

| Policy | Behavior |
|--------|----------|
| `allowlist` | Only groups in `groups` config (default) |
| `open` | Any group |
| `disabled` | No group messages |

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

## Telegram

### Quick Setup

1. Create bot via **@BotFather** (`/newbot`)
2. Configure token and DM policy
3. Start gateway and approve first DM

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

**Env fallback:** `TELEGRAM_BOT_TOKEN` (default account only; named accounts must use `botToken` or `tokenFile`).

Telegram does **not** use `openclaw channels login telegram`; set the token in config/env, then start the gateway.

### Pairing Flow

```bash
openclaw gateway
openclaw pairing list telegram
openclaw pairing approve telegram <CODE>
```

Pairing codes expire after 1 hour.

### BotFather Settings

- `/setprivacy` — Toggle privacy mode (group message visibility)
- `/setjoingroups` — Allow/deny group adds

After toggling privacy mode, remove and re-add the bot in each group.

### Streaming Modes (Telegram)

Canonical key: `channels.telegram.streaming` (nested `{ mode, ... }`).

| Mode | Behavior |
|------|----------|
| `off` | No preview streaming; final message only |
| `partial` | Single preview message edited with latest text; finalized in place |
| `block` | Preview rotates into new messages at chunk threshold (default 800 chars) |
| `progress` | **Default.** Editable status draft during generation (tool progress); final answer sent as normal message |

```json5
// Default behavior (progress mode — no config needed)
{ channels: { telegram: { streaming: { mode: "progress" } } } }

// Stream answer text into preview (pre-2026.4.x behavior)
{ channels: { telegram: { streaming: { mode: "partial" } } } }

// Disable preview entirely
{ channels: { telegram: { streaming: { mode: "off" } } } }
```

**Tool-progress lines** are shown by default in all preview modes. Control them:

```json5
{
  channels: {
    telegram: {
      streaming: {
        mode: "partial",
        preview: {
          toolProgress: true,       // default: true; set false to hide
          commandText: "status",    // default: "status"; "raw" shows command text
        },
      },
    },
  },
}
```

**Progress mode options:**

```json5
{
  channels: {
    telegram: {
      streaming: {
        mode: "progress",
        progress: {
          toolProgress: true,
          commandText: "status",
          commentary: false,        // default; set true for assistant commentary in draft
          maxLines: 8,              // max compact progress lines
          maxLineChars: 120,        // max chars per line
          label: "auto",            // draft title; custom string or false to hide
        },
      },
    },
  },
}
```

**Legacy keys** (`streamMode`, boolean `streaming`): rewritten to `streaming.mode` by `openclaw doctor --fix`; not read at runtime.

### Group Allowlists (Telegram)

- **Which groups:** `channels.telegram.groups` (explicit chat IDs or `"*"`)
- **Which senders in groups:** `groupAllowFrom` (numeric user IDs; falls back to `allowFrom` if unset)
- Negative supergroup IDs (`-1001234567890`) go under `groups`, NOT `groupAllowFrom`
- `dmPolicy: "allowlist"` with empty `allowFrom` blocks all DMs and is rejected by validation

```json5
{
  channels: {
    telegram: {
      allowFrom: ["123456789"],           // your numeric user ID
      groupPolicy: "allowlist",
      groups: {
        "-1001234567890": {
          requireMention: true,
        },
      },
    },
  },
}
```

### Dashboard Mini App

`/dashboard` in a DM opens the Control UI as a Telegram WebApp. Requires:
- `gateway.tailscale.mode: "serve"` or `"funnel"` for HTTPS URL
- Numeric Telegram user ID in `allowFrom` or `commands.ownerAllowFrom`
- Works in DMs only; groups reply with "open in DM"

### Runtime Notes

- Long polling via grammY runner with per-chat/per-thread sequencing
- One active poller per bot token (guarded against concurrent gateways)
- Polling watchdog restarts after 120s without completed `getUpdates` liveness
- Telegram Bot API has no read-receipt support
- Bot identity cached for up to 24h after startup; changing token clears cache
- Routing is deterministic: Telegram inbound replies back to Telegram

---

## WhatsApp

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
openclaw channels login --channel whatsapp
openclaw channels login --channel whatsapp --account work
openclaw pairing list whatsapp
openclaw pairing approve whatsapp <CODE>
```

---

## Discord

### Quick Config

```json5
{
  channels: {
    discord: {
      enabled: true,
      botToken: "OTk...",
      applicationId: "123456789012345678",
      dmPolicy: "pairing",
      groups: { "*": { requireMention: true } },
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

### Streaming (Discord)

Default: `off` when `streaming` is unset. Supports `partial`, `block`, and `progress` modes. In `progress` mode, appends a `-#` activity receipt (tool/thought counts + elapsed time) to the final answer and deletes the status draft.

---

## Slack

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

### Streaming (Slack)

Default: `progress` (Block Kit session card). Supports native streaming API (`chat.startStream`/`append`/`stop`) when `streaming.nativeTransport: true` (default) and `mode: "partial"`.

---

## Streaming and Chunking (Cross-Channel)

### Two Independent Layers

1. **Block streaming:** emit completed blocks as normal channel messages
2. **Preview streaming:** update a temporary preview message (send + edits)

There is **no true token-delta streaming** to channel messages today.

### Block Streaming Controls (`agents.defaults`)

| Key | Values | Default |
|-----|--------|---------|
| `blockStreamingDefault` | `"on"` / `"off"` | `"off"` |
| `blockStreamingBreak` | `"text_end"` / `"message_end"` | — |
| `blockStreamingChunk` | `{ minChars, maxChars, breakPreference? }` | — |
| `blockStreamingCoalesce` | `{ minChars?, maxChars?, idleMs? }` | — |

Channel override: `*.streaming.block.enabled` forces block streaming per channel.

### Preview Streaming Modes

| Mode | Behavior |
|------|----------|
| `off` | Disable preview |
| `partial` | Single preview replaced with latest text |
| `block` | Preview updates in chunked/appended steps |
| `progress` | Progress/status preview during generation, final answer at completion |

### Channel Defaults

| Channel | Default Preview Mode |
|---------|---------------------|
| Telegram | `progress` |
| Slack | `progress` |
| Discord | `off` |
| Mattermost | `partial` |
| MS Teams | `partial` |

### Legacy Key Migration

| Channel | Legacy Keys | Migration |
|---------|-------------|-----------|
| Telegram | `streamMode`, scalar/boolean `streaming` | `openclaw doctor --fix` → `streaming.mode` |
| Discord | `streamMode`, boolean `streaming` | `openclaw doctor --fix` → `streaming.mode` |
| Slack | `streamMode`, boolean `streaming`, `nativeStreaming` | `openclaw doctor --fix` → `streaming.mode` + `streaming.nativeTransport` |
| Matrix | scalar/boolean `streaming` | `openclaw doctor --fix` → `streaming.mode` |

---

## Multi-Account

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "main-bot-token",
      accounts: {
        work: { botToken: "work-bot-token" },
      },
    },
  },
}
```

---

## Troubleshooting

```bash
openclaw channels status --probe    # Channel status
openclaw doctor                      # Diagnostics
openclaw doctor --fix                # Auto-repair config keys
openclaw logs --follow               # Live logs
```
