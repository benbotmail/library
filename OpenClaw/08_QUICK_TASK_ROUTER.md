---
summary: "Quick task routing — what to configure for common OpenClaw tasks"
read_when:
  - Setting up a specific feature
  - Need fast pointers to the right config keys
title: "Quick Task Router"
---

# Quick Task Router

## "Set up a new channel"

1. Add provider config (if not already done):
```json5
{ models: { providers: { openai: { apiKey: "${OPENAI_API_KEY}" } } } }
```

2. Add channel config under `channels.<platform>`:
```json5
{ channels: { telegram: { enabled: true, botToken: "..." } } }
```

3. Restart: `openclaw gateway restart`

For WeChat: `openclaw plugins install "@tencent-weixin/openclaw-weixin"` then `openclaw channels login --channel openclaw-weixin`

## "Enable streaming replies"

Telegram:
```json5
{ channels: { telegram: { streaming: "partial" } } }  // off | partial | block | progress
```

Discord:
```json5
{ channels: { discord: { streaming: "partial" } } }  // progress maps to partial
```

Slack:
```json5
{ channels: { slack: { streaming: { mode: "partial", nativeTransport: true } } } }
```

## "Set up heartbeat"

```json5
{
  agents: {
    defaults: {
      heartbeat: {
        every: "30m",
        target: "none",
        directPolicy: "allow",  // allow (default) | block
        isolatedSession: false,
        lightContext: false,
      },
    },
  },
}
```

- `directPolicy: "block"` prevents heartbeat from sending DMs
- `isolatedSession: true` reduces token cost from ~100K to ~2-5K per heartbeat
- `lightContext: true` keeps only `HEARTBEAT.md` from bootstrap

## "Enable active memory"

```json5
{
  plugins: {
    entries: {
      "active-memory": {
        enabled: true,
        config: {
          agents: ["main"],
          queryMode: "recent",
          promptStyle: "balanced",
          timeoutMs: 15000,
          modelFallback: "google/gemini-3-flash",
        },
      },
    },
  },
}
```

Then: `openclaw gateway restart`
Debug: `/verbose on` + `/trace on`

## "Set up a cron reminder"

Cron jobs are managed via the `cron` tool or chat commands.

One-shot reminder:
```json5
{
  schedule: { kind: "at", at: "2026-04-19T15:00:00Z" },
  payload: { kind: "systemEvent", text: "Reminder: team standup in 5 minutes" },
  sessionTarget: "main",
}
```

Recurring task:
```json5
{
  schedule: { kind: "cron", expr: "0 9 * * MON", tz: "UTC" },
  payload: { kind: "agentTurn", message: "Check emails and summarize urgent items" },
  sessionTarget: "isolated",
}
```

## "Configure memory search"

```json5
{
  agents: {
    defaults: {
      memorySearch: {
        provider: "openai",
        model: "text-embedding-3-small",
        fallback: "gemini",
      },
    },
  },
}
```

Alternative providers: `ollama` (local), `voyage`, `bedrock`, `lmstudio`, `mistral`, `copilot`.

Check health: `openclaw memory status --deep`

## "Set up exec approvals"

```json5
{
  channels: {
    discord: {
      execApprovals: {
        enabled: "auto",
        approvers: ["YOUR_DISCORD_USER_ID"],
        target: "dm",
      },
    },
  },
}
```

## "Block outbound messages to certain contexts"

```json5
{
  channels: {
    telegram: {
      sendPolicy: {
        deny: [
          { chatType: "group" },
          { keyPrefix: "-100123" },
        ],
      },
    },
  },
}
```

Note: `sendPolicy` deny suppresses delivery, not inbound processing.

## "Set up dreaming"

```json5
{
  plugins: {
    entries: {
      "memory-core": {
        config: {
          dreaming: {
            enabled: true,
            storage: { mode: "separate" },
          },
        },
      },
    },
  },
}
```

## "Configure a local model"

```json5
{
  models: {
    providers: {
      ollama: {
        baseUrl: "http://localhost:11434/v1",
        api: "openai-completions",
        models: [{ id: "llama3", name: "Llama 3" }],
      },
    },
  },
  agents: {
    defaults: {
      experimental: { localModelLean: true },
    },
  },
}
```

## "Set up TTS"

```json5
{
  messages: {
    tts: {
      auto: "always",
      provider: "openai",
      voice: "alloy",
    },
  },
}
```

Providers: `openai`, `elevenlabs`, `gemini`, `microsoft` (no API key needed), `minimax`.

## "Set up browser"

```json5
{
  browser: {
    enabled: true,
    defaultProfile: "openclaw",
    profiles: {
      openclaw: { cdpPort: 18800, color: "#FF4500" },
      remote: { cdpUrl: "wss://production-sfo.browserless.io?token=<KEY>" },
    },
  },
}
```

- Local: `openclaw browser start`
- Remote CDP: set `cdpUrl` in profile config
- Node proxy: zero-config if node host is on browser machine
- Browserless/Browserbase: use WSS URL as `cdpUrl`

## "Override model per channel/topic"

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

## "Set up WeChat"

```bash
openclaw plugins install "@tencent-weixin/openclaw-weixin"
openclaw config set plugins.entries.openclaw-weixin.enabled true
openclaw gateway restart
openclaw channels login --channel openclaw-weixin
# Scan QR, then approve pairing:
openclaw pairing list openclaw-weixin
openclaw pairing approve openclaw-weixin <CODE>
```

## "Configure context visibility"

```json5
{
  channels: {
    defaults: {
      contextVisibility: "allowlist",  // all | allowlist | allowlist_quote
    },
    telegram: {
      contextVisibility: "all",  // per-channel override
    },
  },
}
```