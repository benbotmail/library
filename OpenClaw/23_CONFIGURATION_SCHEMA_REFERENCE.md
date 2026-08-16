# Configuration Schema Reference

Core configuration sections with examples for common patterns.

Run `openclaw config schema` for the full schema or `openclaw config.schema.lookup <path>` for specific fields.

## Root-level structure

```json5
{
  // Schema identifier (optional, ignored by Gateway)
  $schema: "...",

  // Agents configuration
  agents: { ... },

  // Channels configuration
  channels: { ... },

  // Messages configuration
  messages: { ... },

  // Plugins configuration
  plugins: { ... },

  // Cron jobs
  cron: { ... },

  // Hooks
  hooks: { ... },

  // Gateway settings
  gateway: { ... },

  // Tool permissions
  tools: { ... },

  // Security settings
  security: { ... },

  // Network settings
  network: { ... },

  // UI settings
  ui: { ... },

  // Logging settings
  logging: { ... },
}
```

## Agents configuration

### Default agent settings

```json5
{
  agents: {
    defaults: {
      // Workspace directory
      workspace: "~/.openclaw/workspace",

      // Primary model
      model: {
        primary: "anthropic/claude-sonnet-4-5",
        fallbacks: ["openai/gpt-4o"],
      },

      // Model catalog with aliases
      models: {
        "anthropic/claude-sonnet-4-5": {
          alias: "Sonnet",
        },
        "openai/gpt-4o": {
          alias: "GPT",
        },
      },

      // Image max dimension for downscaling
      imageMaxDimensionPx: 1200,

      // Heartbeat configuration
      heartbeat: {
        enabled: true,
        every: "30m",
      },

      // Session configuration
      sessions: {
        maxAgeDays: 30,
      },

      // Context configuration
      context: {
        maxTokens: 4000,
        includeMemory: true,
        includeUser: true,
        includeSoul: true,
      },
    },
  },
}
```

### Multi-agent routing

```json5
{
  agents: {
    routing: [
      {
        channel: "discord",
        match: { guild: "123456" },
        agentId: "codex",
      },
      {
        channel: "telegram",
        match: { username: "myusername" },
        agentId: "pi",
      },
    ],
  },
}
```

## Channels configuration

### WhatsApp

```json5
{
  channels: {
    whatsapp: {
      enabled: true,

      // DM access policy
      dmPolicy: "pairing", // pairing | allowlist | open | disabled

      // Allowlisted numbers
      allowFrom: ["+15555550123"],

      // Group chat policy
      groups: {
        "*": { groupPolicy: "requireMention" },
        "123456@g.us": { groupPolicy: "allowlist" },
      },

      // Business account ID (if applicable)
      businessAccountId: "YOUR_BIZ_ID",

      // Metadata
      pushName: "OpenClaw",
      profilePictureUrl: "https://...",
    },
  },
}
```

### Telegram

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11",
      dmPolicy: "pairing",
      allowFrom: ["tg:123456789"],
      groups: {
        "*": { groupPolicy: "requireMention" },
      },
      // Optional: restrict to specific channels
      channelAllowlist: ["tg:-100123456789"],
    },
  },
}
```

### Discord

```json5
{
  channels: {
    discord: {
      enabled: true,
      botToken: "MTIzNDU2Nzg5MA.GhIjKl.MnOpQrStUvWxYzAbCdEfGhIjKlMnOpQrStUv",
      dmPolicy: "allowlist",
      allowFrom: ["discord:123456789"],
      groups: {
        "*": { groupPolicy: "requireMention" },
        // Specific server overrides
        "guild:123456": { groupPolicy: "allowlist" },
      },
      // Intents (required for message events)
      intents: ["Guilds", "GuildMessages", "MessageContent"],
    },
  },
}
```

### Slack

```json5
{
  channels: {
    slack: {
      enabled: true,
      botToken: "xoxb-YOUR-BOT-TOKEN-HERE",
      dmPolicy: "allowlist",
      allowFrom: ["slack:U12345678"],
      groups: {
        "*": { groupPolicy: "requireMention" },
      },
      appToken: "xapp-YOUR-APP-TOKEN-HERE",
    },
  },
}
```

## Messages configuration

```json5
{
  messages: {
    // Group chat behavior
    groupChat: {
      mentionPatterns: ["@openclaw", "@bot", "!assistant"],
    },

    // Reaction behavior
    reactions: {
      enabled: true,
      throttleMs: 1000,
    },

    // Media handling
    media: {
      maxFileSizeBytes: 25 * 1024 * 1024, // 25 MB
      allowedTypes: ["image/jpeg", "image/png", "image/gif", "audio/*"],
    },

    // Reply behavior
    reply: {
      quoteOriginal: false,
      includeContext: false,
    },
  },
}
```

## Plugins configuration

### Built-in plugins

```json5
{
  plugins: {
    entries: {
      brave: {
        enabled: true,
        config: {
          webSearch: {
            apiKey: "YOUR_BRAVE_API_KEY",
          },
        },
      },
      github: {
        enabled: true,
        config: {
          // Auth via git credential store or PAT
        },
      },
      weather: {
        enabled: true,
        config: {
          defaultLocation: "New York",
        },
      },
    },
  },
}
```

### Skill plugins

```json5
{
  plugins: {
    entries: {
      "coding-agent": {
        enabled: true,
        config: {
          defaultAgent: "codex",
          runtime: "acp",
        },
      },
      "gh-issues": {
        enabled: true,
        config: {
          notifyChannel: "-100123456789",
        },
      },
    },
  },
}
```

## Cron configuration

```json5
{
  cron: {
    jobs: [
      {
        name: "morning-check",
        enabled: true,
        schedule: {
          cron: "0 9 * * *", // 9 AM daily
        },
        payload: {
          type: "systemEvent",
          text: "Read HEARTBEAT.md if it exists. Follow it strictly. If nothing needs attention, reply HEARTBEAT_OK.",
        },
      },
      {
        name: "weekly-reminder",
        enabled: true,
        schedule: {
          cron: "0 10 * * 1", // 10 AM Monday
        },
        payload: {
          type: "agentTurn",
          message: "Weekly project check-in. Review git status and pending issues.",
        },
      },
    ],
  },
}
```

## Hooks configuration

```json5
{
  hooks: {
    beforeAgentTurn: [
      {
        name: "log-inbound",
        enabled: true,
        action: {
          type: "exec",
          command: "echo '[{now}] Inbound message from {sender}' >> ~/openclaw-events.log",
        },
      },
    ],
    afterAgentTurn: [
      {
        name: "log-outbound",
        enabled: true,
        action: {
          type: "exec",
          command: "echo '[{now}] Response sent to {sender}' >> ~/openclaw-events.log",
        },
      },
    ],
  },
}
```

## Gateway settings

```json5
{
  gateway: {
    // Port to listen on
    port: 18789,

    // Host binding
    host: "127.0.0.1", // 0.0.0.0 for public access

    // TLS/SSL (if using reverse proxy)
    tls: {
      enabled: false,
      key: "/path/to/key.pem",
      cert: "/path/to/cert.pem",
    },

    // Session cleanup
    cleanup: {
      intervalHours: 24,
      maxAgeDays: 30,
    },

    // Rate limiting
    rateLimit: {
      enabled: true,
      maxPerMinute: 30,
      maxPerHour: 500,
    },
  },
}
```

## Tool permissions

```json5
{
  tools: {
    // Default permissions
    defaults: {
      exec: {
        elevated: true, // Requires approval
      },
      browser: {
        enabled: true,
      },
      webSearch: {
        enabled: true,
      },
      sessions_spawn: {
        enabled: true,
      },
    },

    // Per-channel overrides
    channels: {
      discord: {
        exec: { elevated: true },
        sessions_spawn: { enabled: false }, // Disable for Discord
      },
    },
  },
}
```

## Security settings

```json5
{
  security: {
    // Secret storage
    secrets: {
      backend: "file", // file | keychain | gopass
      path: "~/.openclaw/secrets",
    },

    // Secret egress proxy (default-off; new 2026.8.x)
    // Substitutes shared-store `secret` sentinels at a loopback proxy on egress
    egressProxy: {
      enabled: false,           // requires Gateway restart to change
      bypassHosts: [],          // exact hostnames for blind CONNECT tunnels (cert-pinned clients)
    },

    // Approval policy
    approvals: {
      requireForElevated: true,
      timeoutMinutes: 30,
      autoApprove: [],
    },

    // Sandboxing
    sandbox: {
      enabled: false,
      workspace: "/tmp/openclaw-sandbox",
      allowNetwork: true,
    },
  },
}
```

## Network settings

```json5
{
  network: {
    // Proxy configuration
    proxy: {
      http: "http://proxy.example.com:8080",
      https: "http://proxy.example.com:8080",
      noProxy: ["localhost", "127.0.0.1"],
    },

    // Timeout settings
    timeouts: {
      connectMs: 10000,
      readMs: 30000,
      writeMs: 30000,
    },

    // User agent
    userAgent: "OpenClaw/1.0",
  },
}
```

## UI settings

```json5
{
  ui: {
    // Web Control UI
    web: {
      enabled: true,
      theme: "auto", // auto | light | dark
      showRawLogs: false,
    },

    // CLI output
    cli: {
      verbose: false,
      color: true,
      compact: false,
    },
  },
}
```

## Logging settings

```json5
{
  logging: {
    // Log level
    level: "info", // error | warn | info | debug | trace

    // Log file
    file: {
      enabled: true,
      path: "~/.openclaw/logs/gateway.log",
      maxSizeBytes: 10 * 1024 * 1024,
      maxFiles: 10,
    },

    // Console logging
    console: {
      enabled: true,
      format: "pretty", // pretty | json
    },
  },
}
```

## Schema lookup

Look up any config path:

```bash
# Full schema
openclaw config schema

# Specific field
openclaw config.schema.lookup agents.defaults.model.primary

# Nested field
openclaw config.schema.lookup channels.whatsapp.dmPolicy
```

## Validation rules

### Strict validation
- Unknown root keys are rejected (except `$schema`)
- Unknown nested keys are rejected
- Type mismatches are rejected
- Enum values must match schema

### Common validation errors

**Error:** `Unknown key: channels.whatsapp.enablde`
**Fix:** Typo; should be `enabled`

**Error:** `Type mismatch: expected boolean, got string`
**Fix:** Check value type (remove quotes for boolean/number)

**Error:** `Invalid enum value: channels.whatsapp.dmPolicy = "public"`
**Fix:** Use valid value: `"pairing" | "allowlist" | "open" | "disabled"`

## Provenance
- **Source:** `src/config/schema.ts`, docs/gateway/configuration.md
- **Last validated:** 2026-03-18 (against openclaw@latest from GitHub)
