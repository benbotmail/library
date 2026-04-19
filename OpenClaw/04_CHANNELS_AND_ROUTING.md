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

- `contextVisibility`: controls supplemental context visibility. `all` (default), `allowlist` (only allowlisted senders), `allowlist_quote` (same but keeps explicit quote/reply context). Per-channel override: `channels.<channel>.contextVisibility`.
- `heartbeat`: shared display config for heartbeat channel reports.

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
      groups: {
        "-1001234567890": {
          topics: {
            "99": { requireMention: false, skills: ["search"] },
          },
        },
      },
      customCommands: [{ command: "backup", description: "Git backup" }],
      network: { autoSelectFamily: true, dnsResultOrder: "ipv4first" },
      proxy: "socks5://localhost:9050",
      webhookUrl: "https://example.com/telegram-webhook",
    },
  },
}
```

- Multi-account support with `accounts` block; `defaultAccount` for explicit default
- `configWrites: false` blocks Telegram-initiated config writes
- Topic-level config inside `groups.<id>.topics.<topicId>`
- Topic names cached and persisted across restarts
- Stream previews use `sendMessage` + `editMessageText`
- ACP bindings for forum topics with canonical `chatId:topic:topicId`
- `reactionNotifications: "own" | "off" | "all"`
- Directory config for sender resolution

## Discord

```json5
{
  channels: {
    discord: {
      enabled: true,
      token: { source: "env", provider: "default", id: "DISCORD_BOT_TOKEN" },
      streaming: "off",  // off | partial | block | progress (progress maps to partial)
      threadBindings: { enabled: true, idleHours: 24, maxAgeHours: 0 },
      execApprovals: {
        enabled: "auto",
        approvers: ["userId"],
        target: "dm",  // dm | channel | both
      },
      voice: { enabled: true, daveEncryption: true },
      guilds: {
        "123456789012345678": {
          slug: "friends-of-openclaw",
          channels: {
            help: { allow: true, requireMention: true },
          },
        },
      },
    },
  },
}
```

- SecretRef supported for `token` (env/file/exec providers)
- Native command plugin dispatch for slash commands
- Inbound deduplication across restarts
- `execApprovals`: Discord-native approval delivery
- `threadBindings`: thread-bound session routing with idle/max-age
- `voice`: voice channel conversations with DAVE encryption
- Directory config for member/role resolution
- Outbound adapter: native status replies, no relay

## Slack

```json5
{
  channels: {
    slack: {
      enabled: true,
      mode: "socket",  // socket (default) | http
      appToken: "xapp-...",
      botToken: "xoxb-...",
      streaming: { mode: "partial", nativeTransport: true },
      execApprovals: { enabled: "auto", approvers: ["U123"], target: "dm" },
    },
  },
}
```

- Socket mode: `botToken` + `appToken` (with `connections:write`)
- HTTP mode: `botToken` + `signingSecret` + `webhookPath`
- `typingReaction`: temporary reaction while reply is running
- Native streaming via Slack streaming API (reply thread target required; DMs use typingReaction)
- Thread session isolation: `thread.historyScope` per-thread or shared
- Message tool API: full message lifecycle management
- Media handling: file uploads, attachment processing
- Streaming dispatch: partial streaming to reply threads

## WhatsApp

- Runs through Baileys Web (gateway's web channel)
- Multi-account support with `accounts` block and `defaultAccount`
- `sendReadReceipts` controls blue ticks
- Group session keys: isolated sessions per group JID
- Inbound policy: configurable access control per sender
- Access control refactored: `dmPolicy`, `allowFrom`, `groupPolicy`, `groupAllowFrom`
- Creds written atomically, flushed before reconnect socket open
- Transport honors standard proxy env vars (`HTTPS_PROXY`, `HTTP_PROXY`, `NO_PROXY`)

## Matrix

- Bundled plugin (`@openclaw/matrix`)
- **E2EE support**: via `matrix-js-sdk` with SSSS bootstrap
- Setup: `homeserver` + `accessToken` or `homeserver` + `userId` + `password`
- `autoJoin`: `off` (default) | `allowlist` | `always`
- Subagent hooks for automated actions
- Thread binding API for room/thread session routing
- Reaction auth: authorize actions via Matrix reactions
- Cached credentials in `~/.openclaw/credentials/matrix/`

## WeChat

- **External plugin**: `@tencent-weixin/openclaw-weixin`
- QR login via `openclaw channels login --channel openclaw-weixin`
- Direct chats supported; group chats not advertised
- Plugin manages Tencent iLink API, media, context tokens
- Install: `openclaw plugins install "@tencent-weixin/openclaw-weixin"`
- Pairing via `openclaw pairing list openclaw-weixin`
- Plugin version compatibility check at startup

## BlueBubbles

- Recommended for iMessage (bundled plugin)
- Replay missed webhook messages after gateway restart
- Per-message retry cap for wedged messages
- Inbound deduplication across restarts

## Nextcloud Talk

- Bundled plugin
- Monitor runtime for connection health
- Replay guard: deduplicate replayed messages after reconnect

## Twitch

- Bundled plugin, IRC-based chat connection
- Setup surface via channel config

## Model Overrides by Channel

```json5
{
  channels: {
    modelByChannel: {
      telegram: {
        "-1001234567890": "openai/gpt-4.1-mini",
        "-1001234567890:topic:99": "anthropic/claude-sonnet-4-6",
      },
      discord: {
        "123456789012345678": "anthropic/claude-opus-4-6",
      },
    },
  },
}
```

Channel mapping applies when session doesn't already have a model override. Telegram supports topic-level keys.

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

- `sendPolicy.deny`: first match wins. Match by `channel`, `chatType` (`direct|group|channel`), `keyPrefix`, or `rawKeyPrefix`.
- **Important**: `sendPolicy` deny suppresses **delivery**, not inbound processing.

## Routing

Inbound messages are routed to agents by priority:
1. Exact peer match (`bindings` with `peer.kind` + `peer.id`)
2. Parent peer match (thread inheritance)
3. Guild + roles match (Discord)
4. Guild match (Discord)
5. Team match (Slack)
6. Account match
7. Channel match (any account)
8. Default agent

Outbound: replies go to originating channel. Target resolution uses channel-specific ID formats. Delivery queue with recovery for transient failures.

## Broadcast Groups

Run multiple agents for the same peer in parallel:

```json5
{
  broadcast: {
    strategy: "parallel",
    "120363403215116621@g.us": ["alfred", "baerbel"],
  },
}
```