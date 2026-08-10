# OpenClaw Channels Reference

> **Current state:** OpenClaw v2026.8.1 · upstream `cd7b7f6`

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
dmPolicy: "pairing"   // pairing | allowlist | open | disabled
```

| Policy | Behavior |
|--------|----------|
| `pairing` | Unknown senders get pairing code (default) |
| `allowlist` | Only senders in `allowFrom` |
| `open` | All inbound DMs (requires `allowFrom: ["*"]`) |
| `disabled` | Ignore all DMs |

### Group Policy

```json5
groupPolicy: "requireMention"   // open | requireMention | allowlist | disabled
```

### Channel Defaults (inherited)

```json5
{
  channels: {
    defaults: {
      groupPolicy: "requireMention",
      contextVisibility: "always",
      heartbeatVisibility: { enabled: false },
      botLoopProtection: { /* pair-loop guard */ },
      implicitMentions: { /* implicit mention policy */ },
    },
  },
}
```

---

## Telegram

### Quick Config

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "123456:ABC-DEF",
      // tokenFile: "/path/to/token-file",  // alternative to botToken
      dmPolicy: "pairing",
      groups: { "*": { groupPolicy: "requireMention" } },
    },
  },
}
```

### Streaming / Preview Modes

```json5
{
  channels: {
    telegram: {
      streaming: {
        // Preview streaming mode (how live agent responses are shown)
        mode: "progress",
        // off:     no preview updates
        // partial: update one preview message in place
        // block:   emit larger chunked preview updates
        // progress: progress/status preview with tool activity

        chunkMode: "text",         // text chunking mode for outbound delivery
        nativeTransport: false,    // prefer Telegram native streaming transport

        preview: {
          minChars: undefined,     // min chunk size before sending update
          maxChars: undefined,     // max chunk size before forcing update
          breakPreference: "paragraph",
          toolProgress: true,
          commandText: "raw",      // raw | status
        },

        progress: {
          label: "auto",           // "auto" | false | custom string
          maxLines: 8,             // max progress lines below label
          maxLineChars: 120,
          render: "text",          // text | rich
          toolProgress: true,
          commandText: "raw",      // raw | status
          commentary: false,       // include assistant preamble text
          narration: true,         // utility-model narration of tool activity
        },

        block: {
          enabled: false,          // chunked block-reply delivery
          chunk: { breakPreference: "paragraph" },
          toolProgress: true,
          commandText: "raw",
        },
      },
    },
  },
}
```

### Capabilities

```json5
{
  channels: {
    telegram: {
      capabilities: {
        inlineButtons: "dm",   // off | dm | group | all | allowlist
      },
    },
  },
}
```

### Reactions

```json5
{
  channels: {
    telegram: {
      reactions: {
        enabled: true,
        listen: "off",     // off | own | all — which user reactions trigger notifications
        send: "ack",       // off | ack | minimal | extensive — agent reaction behavior
      },
    },
  },
}
```

### Rich Messages (Bot API 10.2)

```json5
{
  channels: {
    telegram: {
      richMessages: false,
      // Enable for native tables, details, and rich media via sendRichMessage.
      // WARNING: Web/Desktop and older mobile clients may not support these.
    },
  },
}
```

### Network

```json5
{
  channels: {
    telegram: {
      network: {
        dnsResultOrder: "ipv4first",   // ipv4first | verbatim
        autoSelectFamily: undefined,    // override Node autoSelectFamily
        dangerouslyAllowPrivateNetwork: false,
      },
      proxy: "socks5://...",
      apiRoot: "https://my-bot-api.example.com",
      trustedLocalFileRoots: ["/var/lib/telegram-bot-api/files"],
    },
  },
}
```

### Error Policy

```json5
{
  channels: {
    telegram: {
      errorPolicy: "always",       // always | once | silent
      silentErrorReplies: false,   // send error replies without notification sound
    },
  },
}
```

### Groups & Topics

```json5
{
  channels: {
    telegram: {
      groups: {
        "*": {
          groupPolicy: "requireMention",
          requireMention: true,
          ingest: false,            // emit hooks for mention-skipped messages
          skills: ["github"],       // limit skills; omit = all, empty = none
          enabled: true,
          allowFrom: [],
          systemPrompt: "",
          disableAudioPreflight: false,
          errorPolicy: "always",
          topics: {
            "*": { requireMention: true },
            "42": {
              agentId: "codex",
              systemPrompt: "Topic-specific prompt",
              groupPolicy: "open",
              skills: [],
              allowFrom: [123456],
            },
          },
        },
      },
    },
  },
}
```

### Direct DM Config

```json5
{
  channels: {
    telegram: {
      direct: {
        "123456789": {
          dmPolicy: "open",
          autoTopicLabel: true,    // LLM-based topic naming (default: true)
          topics: { /* same as group topics */ },
        },
      },
    },
  },
}
```

### Thread Bindings

```json5
{
  channels: {
    telegram: {
      threadBindings: {
        enabled: false,
        spawnSessions: false,
        defaultSpawnContext: "isolated",   // isolated | fork
      },
    },
  },
}
```

### Multi-Account

```json5
{
  channels: {
    telegram: {
      accounts: {
        work: {
          botToken: "work-bot-token",
          // ...all same fields as top-level
        },
      },
      defaultAccount: "work",
    },
  },
}
```

### Actions

```json5
{
  channels: {
    telegram: {
      actions: {
        reactions: true,
        sendMessage: true,
        poll: false,            // requires sendMessage
        deleteMessage: true,
        editMessage: true,
        sticker: false,
        createForumTopic: false,
        editForumTopic: false,
      },
    },
  },
}
```

---

## WhatsApp

### Quick Config

```json5
{
  channels: {
    whatsapp: {
      dmPolicy: "pairing",
      allowFrom: ["+15551234567"],
      groups: {
        "*": { groupPolicy: "requireMention" },
      },
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
      botToken: "...",
      dmPolicy: "pairing",
      allowFrom: ["discord:123456789"],
      groups: {
        "*": { groupPolicy: "requireMention" },
        "guild:123456": { groupPolicy: "allowlist" },
      },
      threadBindings: {
        enabled: true,
        spawnSessions: true,
        defaultSpawnContext: "isolated",
      },
    },
  },
}
```

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
      dmPolicy: "pairing",
      groups: { "*": { groupPolicy: "requireMention" } },
    },
  },
}
```

---

## Signal

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

---

## Troubleshooting

```bash
openclaw channels status --probe   # channel status
openclaw doctor                    # diagnostics
openclaw logs --follow             # live logs
```

---

## Provenance

- **Source files:** `src/config/types.telegram.ts`, `src/config/types.channels.ts`, `src/config/types.base.ts`, `src/channels/streaming.ts`, `src/channels/streaming-config-readers.ts`
- **Upstream commit:** `cd7b7f639da0d26424b52f3ffa2391f81acb5040`
- **OpenClaw version:** `2026.8.1`
- **Last validated:** 2026-08-10
