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
  schedule: { kind: "at", at: "2026-04-16T15:00:00Z" },
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
        fallback: "gemini",  // optional failover
      },
    },
  },
}
```

For local models:
```json5
{
  agents: {
    defaults: {
      memorySearch: {
        provider: "ollama",
        model: "nomic-embed-text",
      },
    },
  },
}
```

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
          { chatType: "group" },         // block all groups
          { keyPrefix: "-100123" },       // block specific chat prefix
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
            storage: { mode: "separate" },  // default: phase blocks separate from daily files
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
      experimental: {
        localModelLean: true,  // trim tools for small contexts
      },
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
