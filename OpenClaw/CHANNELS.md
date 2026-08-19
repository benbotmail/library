# OpenClaw Channels Reference

> Messaging channel setup, streaming modes, and configuration — current as of 2026-08-19 (upstream `7a82d8b0f25`).

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

### Group session routing (`session.groupScope`)

Groups and channels get isolated sessions per room under the default `session.groupScope: "per-group"`. Set the global `session.groupScope: "main"` to route all non-direct peers into the agent's **main session**, or override selected rooms via `bindings[].session.groupScope` (binding override wins over the global value):

```json5
{
  bindings: [
    {
      agentId: "main",
      match: { channel: "slack", peer: { kind: "channel", id: "C0123TEAM" } },
      session: { groupScope: "main" },
    },
  ],
}
```

This changes shared **context** only — group admission, mention gating, and replies still use the originating group or channel. **Sandbox warning:** a room scoped to `main` is a main session and is **not** covered by sandbox `mode: "non-main"`; never merge untrusted/public rooms into main when relying on that boundary.

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

**Webhook mode:** set `channels.telegram.webhookUrl` + `webhookSecret` (optional `webhookPath` default `/telegram-webhook`, `webhookHost` default `127.0.0.1`, `webhookPort` default `8787`). The listener reserves **`/healthz` for health checks** — `webhookPath` must use a different route (existing setups on `/healthz` must pick another path and update the reverse proxy). Default is long polling; the restart watermark persists only after an update dispatches successfully.

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

**Env-only startup:** without a `channels.discord` block, the Gateway does **not** auto-start Discord from `DISCORD_BOT_TOKEN`. Once the block exists, `DISCORD_BOT_TOKEN` is the default-account token fallback. Passing `--ambient-channels` opts into env-only auto-configuration; that path uses `groupPolicy="allowlist"` and logs a warning, even if `channels.defaults.groupPolicy` is `open`.

### Discord voice presence

- `voice.autoJoin[]` — fixed-room presence; with multiple entries per guild, OpenClaw joins the **last** configured channel for that guild.
- `voice.autoJoin[].whenOccupied` (default `false`) — set `true` for occupancy-managed presence: OpenClaw joins on the first human arrival and leaves after the last human departs (bots don't count). Startup and resumed sessions reconcile from Discord's voice-state roster. Occupancy management owns **only** sessions it joined — manual `/vc join`, transcript capture, or other ad-hoc joins are never moved or disconnected when the room empties.
- `voice.allowedChannels` — optional residency allowlist for `/vc join` and auto-join; unset = any authorized voice channel, `[]` = deny all.

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

Default mode: `progress`. In `progress` mode, Slack's **native agent card is the default**: the whole turn is one streamed message interleaving narration with a live plan/task card, finishing with the answer in that same message. The card appears only once a turn does real work (tool/plan activity still running after a short delay); plain questions are answered without one.

Set `channels.slack.streaming.progress.nativeTaskCards: false` to fall back to the Block Kit session card (separate message with title, narration, plan checklist, recent activity, tool/file totals, elapsed time; finalizes to success/error).

Native text streaming API (`chat.startStream`/`append`/`stop`) applies when `streaming.mode: "partial"` with `streaming.nativeTransport: true` (default).

**"Open in OpenClaw" link** on cards appears only when `gateway.publicOrigin` is set and the Control UI is not disabled (`gateway.controlUi.enabled` not `false`). If the Control UI is served under a path prefix, also set `gateway.controlUi.basePath`.

**Env-only startup:** without a `channels.slack` block, the Gateway does **not** auto-start Slack from `SLACK_*` environment variables. Once the block exists, those variables remain default-account credential fallbacks. `--ambient-channels` opts into env-only auto-configuration with `groupPolicy="allowlist"` + warning.

**Presence wakes:** `presenceEvents` (`off|auto|on`, default `off`) polls at most 45 unique workspace-user pairs/min per account, with a durable 8-hour cooldown per account/workspace/user; only `away→active` transitions wake the agent. `presenceEvents.prompt` (new) replaces the default greeting guidance after the event facts — account-level default, per-channel override via `channels.<id>.presenceEvents.prompt`, max 20,000 chars, empty string omits event-specific guidance.

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
