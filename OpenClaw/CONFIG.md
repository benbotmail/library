# OpenClaw Configuration Guide

> Configuration structure, patterns, and examples

## Config Location

```
~/.openclaw/openclaw.json     # Main config (JSON5)
~/.openclaw/credentials/       # Auth credentials
```

## Minimal Config

```json5
{
  agents: {
    defaults: {
      workspace: "~/.openclaw/workspace",
    },
  },
  channels: {
    whatsapp: {
      allowFrom: ["+15555550123"],
    },
  },
}
```

---

## Top-Level Structure

```json5
{
  // Agent defaults
  agents: { ... },

  // Channel connections
  channels: { ... },

  // Session management
  session: { ... },

  // Tool control
  tools: { ... },

  // Browser settings
  browser: { ... },

  // Automation
  cron: { ... },

  // Plugins/extensions
  plugins: { ... },

  // Gateway settings
  gateway: { ... },

  // Logging
  logging: { ... },
}
```

---

## Agents

```json5
{
  agents: {
    defaults: {
      workspace: "~/.openclaw/workspace",
      model: {
        primary: "anthropic/claude-sonnet-4-5",
        fallbacks: ["openai/gpt-5.2"],
      },
      models: {
        "anthropic/claude-sonnet-4-5": { alias: "Sonnet" },
        "openai/gpt-5.2": { alias: "GPT" },
      },
      thinking: "low",        // low | medium | high
      imageModel: { primary: "openai/gpt-4.1-mini" },
      imageGenerationModel: { primary: "google/gemini-3-pro-image-preview" },
    },
    list: [
      {
        id: "work",
        workspace: "~/workspaces/work",
        model: { primary: "anthropic/claude-opus-4-6" },
      },
    ],
  },
}
```

---

## Channels

### DM Policy

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "123:abc",
      dmPolicy: "pairing",    // pairing | allowlist | open | disabled
      allowFrom: ["tg:123"],
    },
  },
}
```

### Group Policy

```json5
{
  channels: {
    discord: {
      groupPolicy: "allowlist",
      groupAllowFrom: ["123456789"],
      groups: {
        "*": { requireMention: true },
        "123456789": { requireMention: false },
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
      enabled: true,
      botToken: "main-token",
      accounts: {
        work: { botToken: "work-token" },
      },
    },
  },
}
```

---

## Session

```json5
{
  session: {
    dmScope: "main",           // main | per-peer | per-channel-peer
    identityLinks: {
      alice: ["telegram:123", "discord:456"],
    },
    reset: {
      mode: "daily",
      atHour: 4,
      idleMinutes: 120,
    },
    maintenance: {
      mode: "enforce",
      pruneAfter: "30d",
      maxEntries: 500,
    },
    sendPolicy: {
      rules: [
        { action: "deny", match: { channel: "discord", chatType: "group" } },
      ],
      default: "allow",
    },
  },
}
```

---

## Tools

```json5
{
  tools: {
    profile: "coding",         // minimal | coding | messaging | full
    allow: ["group:fs", "browser"],
    deny: ["exec"],
    byProvider: {
      "google-antigravity": { profile: "minimal" },
    },
    loopDetection: {
      enabled: true,
      warningThreshold: 10,
    },
  },
}
```

---

## Browser

```json5
{
  browser: {
    enabled: true,
    defaultProfile: "openclaw",
    ssrfPolicy: {
      dangerouslyAllowPrivateNetwork: true,
    },
    profiles: {
      openclaw: { cdpPort: 18800 },
      user: {
        driver: "existing-session",
        attachOnly: true,
      },
    },
  },
}
```

---

## Cron

```json5
{
  cron: {
    enabled: true,
    jobs: [
      {
        id: "daily-report",
        name: "Daily Report",
        schedule: { kind: "cron", expr: "0 9 * * *" },
        payload: {
          kind: "agentTurn",
          message: "Generate daily report",
        },
        sessionTarget: "isolated",
        enabled: true,
      },
    ],
  },
}
```

---

## Gateway

```json5
{
  gateway: {
    port: 18789,
    mode: "local",             // local | remote
    auth: {
      enabled: true,
      tokens: ["secret-token"],
    },
    bind: "loopback",          // loopback | all
  },
}
```

---

## Logging

```json5
{
  logging: {
    level: "info",             // debug | info | warn | error
    file: "~/.openclaw/logs/gateway.log",
    maxSize: "10mb",
    maxFiles: 5,
  },
}
```

---

## CLI Commands

```bash
# Get value
openclaw config get agents.defaults.thinking

# Set value
openclaw config set agents.defaults.thinking high

# Unset value
openclaw config unset agents.defaults.thinking

# Interactive
openclaw configure
```

---

## Validation

- Strict schema validation on startup
- Unknown keys rejected
- Run `openclaw doctor` for diagnostics
- Run `openclaw doctor --fix` for auto-repairs
