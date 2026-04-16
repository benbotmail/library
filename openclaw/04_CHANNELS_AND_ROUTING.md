---
summary: "Channel configuration, routing, DM/group policies, streaming modes, and per-channel defaults"
read_when:
  - Setting up a channel
  - Configuring DM or group policies
  - Understanding streaming or delivery modes
title: "Channels and Routing"
---

# Channels and Routing

## Channel Startup

Each channel starts automatically when its config section exists in `openclaw.json` (unless `enabled: false`). When a provider block is missing entirely, group policy falls back to `allowlist` (fail-closed) with a startup warning.

## DM and Group Policies

All channels share the same DM/group policy model:

| DM Policy | Behavior |
|-----------|----------|
| `pairing` (default) | Unknown senders get a one-time pairing code; owner approves |
| `allowlist` | Only senders in `allowFrom` (or paired allow store) |
| `open` | Allow all inbound DMs (requires `allowFrom: ["*"]`) |
| `disabled` | Ignore all inbound DMs |

| Group Policy | Behavior |
|-------------|----------|
| `allowlist` (default) | Only groups matching the configured allowlist |
| `open` | Bypass group allowlists (mention-gating still applies) |
| `disabled` | Block all group/room messages |

Pairing codes expire after 1 hour. Pending DM pairing requests capped at **3 per channel**.

## Channel Defaults

```json5
{
  channels: {
    defaults: {
      groupPolicy: "allowlist",
      contextVisibility: "all",  // all | allowlist | allowlist_quote
      heartbeat: {
        showOk: false,
        showAlerts: true,
        useIndicator: true,
      },
    },
  },
}
```

- `contextVisibility`: controls supplemental context visibility. `all` (default), `allowlist` (only allowlisted senders), `allowlist_quote` (same but keeps explicit quote/reply context).

## Telegram

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "your-bot-token",
      dmPolicy: "pairing",
      streaming: "partial",  // off | partial | block | progress (default: off)
      replyToMode: "first",  // off | first | all | batched
      retry: { attempts: 3, minDelayMs: 400, maxDelayMs: 30000 },
      // Topic-name cache persists across restarts
      // Binary document captions are sanitized to prevent prompt inflation
      // Command sync is process-local (no cross-process races)
    },
  },
}
```

- Streaming modes: `off` (default), `partial` (sendMessage + editMessageText), `block`, `progress`
- Multi-account support with `accounts` block
- `configWrites: false` blocks Telegram-initiated config writes
- Topic names are cached and persisted across restarts
- Network: `autoSelectFamily`, `dnsResultOrder`, `proxy` support
- Webhook mode available via `webhookUrl`/`webhookSecret`/`webhookPath`

## Discord

```json5
{
  channels: {
    discord: {
      streaming: "off",  // off | partial | block | progress (progress maps to partial)
      threadBindings: { enabled: true, idleHours: 24, maxAgeHours: 0 },
      execApprovals: {
        enabled: "auto",  // true | false | "auto"
        approvers: ["userId"],
        target: "dm",     // dm | channel | both
      },
      voice: { enabled: true, daveEncryption: true },
    },
  },
}
```

- Inbound deduplication across restarts
- Native status replies returned directly (no relay)
- `execApprovals`: Discord-native approval delivery with DM, channel, or both
- `threadBindings`: thread-bound session routing with idle/max-age
- `voice`: voice channel conversations with DAVE encryption

## Slack

```json5
{
  channels: {
    slack: {
      streaming: {
        mode: "partial",
        nativeTransport: true,  // use Slack native streaming API
      },
      execApprovals: {
        enabled: "auto",
        approvers: ["U123"],
        target: "dm",
      },
    },
  },
}
```

- Socket mode requires `botToken` + `appToken`
- HTTP mode requires `botToken` + `signingSecret`
- `typingReaction`: temporary reaction while reply is running
- Native streaming requires a reply thread target (DMs use typingReaction instead)
- Thread session isolation: `thread.historyScope` per-thread or shared

## WhatsApp

- Runs through Baileys Web (gateway's web channel)
- Multi-account support with `accounts` block
- `sendReadReceipts` controls blue ticks
- Creds written atomically, flushed before reconnect socket open
- Doctor contract API for fast-path diagnostics

## BlueBubbles

- Replay missed webhook messages after gateway restart
- Per-message retry cap for wedged messages
- Inbound deduplication across restarts
- Lazy-refresh Private API status on send

## Model Overrides by Channel

```json5
{
  channels: {
    modelByChannel: {
      telegram: {
        "-1001234567890": "openai/gpt-4.1-mini",
        "-1001234567890:topic:99": "anthropic/claude-sonnet-4-6",
      },
    },
  },
}
```

Channel mapping applies when session doesn't already have a model override.

## SendPolicy

```json5
{
  channels: {
    telegram: {
      sendPolicy: {
        deny: [
          { channel: "telegram", chatType: "group" },
        ],
      },
    },
  },
}
```

- `sendPolicy.deny`: first match wins. Match by `channel`, `chatType` (`direct|group|channel`, with legacy `dm` alias), `keyPrefix`, or `rawKeyPrefix`.
- **Important**: `sendPolicy` deny suppresses **delivery**, not inbound processing. The agent still processes the message; only the outbound reply is blocked.

## Routing

Inbound messages are routed to sessions by:
1. Channel + chat/topic ID → session key
2. Thread bindings (Discord threads, Telegram topics)
3. Session reset creates new transcript, keeps config overrides
4. System events preserve shared session route

Outbound delivery:
- Target resolution: channel-specific ID formats (`user:<id>`, `channel:<id>`)
- Delivery queue with recovery for transient failures
- Media path normalization and containment checks
