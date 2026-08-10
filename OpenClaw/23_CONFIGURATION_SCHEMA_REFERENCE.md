# Configuration Schema Reference

> **Current state:** OpenClaw v2026.8.1 · upstream `cd7b7f6`
> Canonical config shapes, keys, and defaults. Run `openclaw config schema` for the full schema or `openclaw config.schema.lookup <path>` for specific fields.

---

## Root-level structure

```json5
{
  $schema: "...",          // optional, ignored by Gateway
  agents: { ... },         // agent definitions, defaults, bindings
  channels: { ... },       // messaging channel sections
  messages: { ... },       // message delivery, queue, prefix config
  plugins: { ... },        // plugin entries
  cron: { ... },           // scheduled jobs
  hooks: { ... },          // runtime hook registration
  gateway: { ... },        // gateway server, auth, UI, discovery
  tools: { ... },          // tool exposure, exec policy, media
  security: { ... },       // audit suppressions, install policy
  approvals: { ... },      // exec/plugin approval forwarding
  session: { ... },        // session keying, reset, thread bindings
  models: { ... },         // model providers, catalog, pricing
  secrets: { ... },        // secret providers and refs
  skills: { ... },         // skill loading
  browser: { ... },        // browser automation
  memory: { ... },         // memory indexing/search
  mcp: { ... },            // MCP client/server config
  tts: { ... },            // text-to-speech defaults
  ui: { ... },             // Control UI, prefs, theme
  logging: { ... },        // log sink, level, rotation
  diagnostics: { ... },    // tracing, stability debugging
  transcripts: { ... },    // transcript persistence
  nodeHost: { ... },       // node-host pairing
  cloudWorkers: { ... },   // opt-in cloud workers
  discovery: { ... },      // network discovery
  talk: { ... },           // voice/talk mode
  proxy: { ... },          // SSRF forward proxy
  env: { ... },            // inline env vars, shellEnv import
  wizard: { ... },         // guided onboarding state
  update: { ... },         // update channel, auto-update
}
```

---

## Agents configuration

### `agents.defaults`

All agent-level defaults. Per-agent entries under `agents.entries[]` override these.

```json5
{
  agents: {
    defaults: {
      // Workspace directory (preferred cwd for agent runs)
      workspace: "~/.openclaw/workspace",

      // Primary model + fallbacks (string or { primary, fallbacks })
      model: {
        primary: "openai/gpt-5.6-sol",
        fallbacks: ["anthropic/claude-sonnet-5"],
      },

      // Built-in model aliases (v2026.8.1)
      // opus → anthropic/claude-opus-5
      // sonnet → anthropic/claude-sonnet-5
      // gpt → openai/gpt-5.4
      // gpt-mini → openai/gpt-5.4-mini
      // gpt-nano → openai/gpt-5.4-nano
      // gemini → google/gemini-3.1-pro-preview
      // gemini-flash → google/gemini-3-flash-preview
      // gemini-flash-lite → google/gemini-3.1-flash-lite

      // Model catalog with per-model overrides
      models: {
        "anthropic/claude-sonnet-5": { alias: "sonnet" },
        "openai/gpt-5.6-sol": { alias: "gpt" },
      },

      // Image/PDF/voice/media model overrides
      imageModel: "google/gemini-3-flash-preview",
      pdfModel: "anthropic/claude-opus-5",
      pdfMaxMb: 10,
      pdfMaxPages: 20,
      utilityModel: "openai/gpt-5.4-mini",

      // Thinking / reasoning / verbose defaults
      thinkingDefault: "low",    // off|minimal|low|medium|high|xhigh|adaptive|max|ultra
      reasoningDefault: "off",   // off|on|stream
      verboseDefault: "off",     // off|on|full
      elevatedDefault: "off",    // off|on|ask|full

      // Context window cap (tokens) — used for status %, runtime estimates
      contextTokens: 200000,

      // Context injection policy for bootstrap files
      contextInjection: "always",  // always|continuation-skip|never
      bootstrapMaxChars: 20000,
      bootstrapTotalMaxChars: 150000,

      // Image handling
      imageMaxDimensionPx: 1200,
      imageQuality: "auto",  // auto|efficient|balanced|high

      // Startup context (bare /new and /reset)
      startupContext: {
        enabled: true,
        applyOn: ["new", "reset"],
        dailyMemoryDays: 2,
        maxFileBytes: 16384,
        maxFileChars: 1200,
        maxTotalChars: 2800,
      },

      // Context pruning (opt-in)
      contextPruning: {
        mode: "off",   // off|cache-ttl
        ttl: "30m",
        tools: { allow: [], deny: [] },
        hardClear: { enabled: false, placeholder: "[pruned]" },
      },

      // Compaction tuning
      compaction: { /* see AgentCompactionConfig */ },

      // Sub-agent defaults
      subagents: {
        delegationMode: "suggest",  // suggest|prefer
        allowAgents: [],
        maxConcurrent: 8,
        maxSpawnDepth: 1,
        maxChildrenPerAgent: 5,
        archiveAfterMinutes: 60,
      },

      // Max concurrent agent runs (global)
      maxConcurrent: 16,

      // ─── Heartbeat ───
      heartbeat: {
        agentId: undefined,       // agent that owns heartbeat runs
        every: "30m",             // interval (duration string)
        activeHours: {
          start: undefined,       // "09:00" (24h, local time)
          end: undefined,         // "23:00" (exclusive; "24:00" = end-of-day)
          timezone: "user",       // "user" | "local" | IANA TZ id
        },
        model: undefined,         // heartbeat model override (provider/model)
        session: undefined,       // session key ("main" or explicit)
        target: "last",           // "last" | "none" | channel id
        directPolicy: "allow",    // "allow" | "block" — blocks DM delivery when "block"
        to: undefined,            // override destination (E.164 for WhatsApp, chat id for Telegram)
        accountId: undefined,     // multi-account channel support
        prompt: undefined,        // override heartbeat prompt body
        timeoutSeconds: undefined, // run timeout (defaults to cadence-capped 600s)
        lightContext: false,      // skip workspace bootstrap files
        isolatedSession: false,   // run without prior conversation history (saves tokens)
      },

      // ─── Block streaming ───
      blockStreamingDefault: "off",     // off|on
      blockStreamingBreak: "text_end",  // text_end|message_end
      blockStreamingChunk: {
        minChars: undefined,
        maxChars: undefined,
        breakPreference: "paragraph",   // paragraph|newline|sentence
      },
      blockStreamingCoalesce: {
        idleMs: undefined,
      },
      humanDelay: {
        minMs: undefined,
        maxMs: undefined,
      },

      // Typing indicators
      typingMode: "instant",    // never|instant|thinking|message
      typingIntervalSeconds: undefined,

      // Timezone for prompt timestamps, heartbeat active hours
      userTimezone: undefined,  // IANA TZ id

      // Tool progress detail
      toolProgressDetail: "explain",  // explain|raw

      // Embedded agent settings
      embeddedAgent: {
        projectSettingsPolicy: "sanitize",  // trusted|sanitize|ignore
        executionContract: "default",       // default|strict-agentic
      },
    },

    // Per-agent entries
    entries: [
      {
        id: "codex",
        identity: { name: "Codex", avatar: "📚" },
        model: { primary: "zai/glm-5" },
        skills: ["github", "coding-agent", "weather"],
        // heartbeat can also be set per-agent
        heartbeat: { every: "30m", directPolicy: "allow" },
      },
    ],
  },
}
```

---

## Channels configuration

### Channel defaults

```json5
{
  channels: {
    defaults: {
      groupPolicy: "requireMention",  // open|requireMention|allowlist|disabled
      contextVisibility: "always",    // always|continuation-skip|never
      heartbeatVisibility: { enabled: false },
      botLoopProtection: { /* ... */ },
      implicitMentions: { /* ... */ },
    },
    // modelByChannel: map provider → channel id/DM peer id → model override
    modelByChannel: {},
  },
}
```

### Telegram

```json5
{
  channels: {
    telegram: {
      enabled: true,
      botToken: "123456:ABC-DEF",   // or use tokenFile for file-based
      // tokenFile: "/path/to/token-file",

      dmPolicy: "pairing",          // pairing|allowlist|open|disabled
      allowFrom: ["tg:123456789"],

      groups: {
        "*": { groupPolicy: "requireMention" },
        "-100123456789": {
          groupPolicy: "open",
          requireMention: false,
          topics: {
            "*": { requireMention: true },
            "42": { agentId: "codex", systemPrompt: "..." },
          },
        },
      },

      // Direct DM config (key is chat ID)
      direct: {
        "123456789": {
          dmPolicy: "open",
          autoTopicLabel: true,
        },
      },

      // ─── Streaming / preview ───
      streaming: {
        mode: "progress",           // off|partial|block|progress
        chunkMode: "text",          // text chunking mode
        nativeTransport: false,     // prefer channel's native streaming
        preview: {
          minChars: undefined,
          maxChars: undefined,
          breakPreference: "paragraph",
          toolProgress: true,
          commandText: "raw",       // raw|status
        },
        progress: {
          label: "auto",            // "auto" | false | custom string
          labels: [],
          maxLines: 8,
          maxLineChars: 120,
          render: "text",           // text|rich
          toolProgress: true,
          commandText: "raw",       // raw|status
          commentary: false,
          narration: true,          // utility-model narration of tool activity
        },
        block: {
          enabled: false,
          chunk: { /* same as preview chunk */ },
          toolProgress: true,
          commandText: "raw",
        },
      },

      // Capabilities
      capabilities: {
        inlineButtons: "dm",  // off|dm|group|all|allowlist
      },

      // Reactions
      reactions: {
        enabled: true,
        listen: "off",         // off|own|all
        send: "ack",           // off|ack|minimal|extensive
      },

      // Rich messages (Bot API 10.2)
      richMessages: false,     // native tables, details — note client compat issues

      // Network
      network: {
        autoSelectFamily: undefined,
        dnsResultOrder: "ipv4first",
        dangerouslyAllowPrivateNetwork: false,
      },

      // Error handling
      errorPolicy: "always",   // always|once|silent
      silentErrorReplies: false,

      // Actions
      actions: {
        reactions: true,
        sendMessage: true,
        poll: false,
        deleteMessage: true,
        editMessage: true,
        sticker: false,
        createForumTopic: false,
        editForumTopic: false,
      },

      // Thread bindings
      threadBindings: {
        enabled: false,
        spawnSessions: false,
        defaultSpawnContext: "isolated",
      },

      // Multi-account
      accounts: {
        work: {
          botToken: "work-bot-token",
          // ... same fields as top-level account
        },
      },

      // Auto topic labeling
      autoTopicLabel: true,

      // Link preview in outbound messages
      linkPreview: true,
    },
  },
}
```

### WhatsApp

```json5
{
  channels: {
    whatsapp: {
      enabled: true,
      dmPolicy: "pairing",
      allowFrom: ["+15551234567"],
      groups: {
        "*": { groupPolicy: "requireMention" },
      },
      // Multi-account support
      accounts: {
        work: { /* ... */ },
      },
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
      botToken: "...",
      dmPolicy: "pairing",
      allowFrom: ["discord:123456789"],
      groups: {
        "*": { groupPolicy: "requireMention" },
      },
      // Thread bindings for session spawning
      threadBindings: {
        enabled: true,
        spawnSessions: true,
        defaultSpawnContext: "isolated",
      },
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
      botToken: "xoxb-...",
      appToken: "xapp-...",
      dmPolicy: "pairing",
      groups: { "*": { groupPolicy: "requireMention" } },
    },
  },
}
```

---

## Messages configuration

```json5
{
  messages: {
    // Ack reaction scope
    ackReactionScope: "group-mentions",  // group-mentions|always|never

    // Visible replies mode
    visibleReplies: "automatic",   // automatic|message_tool

    // Outbound prefix
    prefix: "",   // string | "auto"
    // Template vars: {model}, {modelFull}, {provider}, {thinkingLevel}, {identity.name}

    // Group chat
    groupChat: {
      mentionPatterns: ["@openclaw"],
      unmentionedInbound: "user_request",  // user_request|room_event
      visibleReplies: "automatic",
    },

    // Queue
    queue: {
      mode: "steer",   // steer|queue|drop
      cap: undefined,
      drop: undefined,
      byChannel: {},
    },

    // Inbound debounce
    inboundDebounce: {
      debounceMs: undefined,
      byChannel: {},
    },
  },
}
```

---

## Approvals configuration

```json5
{
  approvals: {
    // Forward exec approvals to chat channels
    exec: {
      enabled: false,
      mode: "session",      // session|targets|both
      agentFilter: [],      // restrict to specific agent IDs
      sessionFilter: [],    // filter by session key patterns
      targets: [
        {
          channel: "telegram",
          to: "123456789",
          accountId: undefined,
          threadId: undefined,
        },
      ],
    },
    // Forward plugin approvals (same shape)
    plugin: { /* same as exec */ },
  },
}
```

---

## Cron configuration

```json5
{
  cron: {
    enabled: true,
    triggers: { enabled: true },
    webhookToken: "secret",          // bearer token for webhook delivery
    sessionRetention: "24h",         // duration string | false
    failureAlert: {
      enabled: false,
      after: 2,                      // alert after N failures
      cooldownMs: 3600000,
      includeSkipped: false,
      mode: "announce",             // announce|webhook
      channel: undefined,
      to: undefined,
    },
    jobs: [
      {
        name: "morning-check",
        enabled: true,
        schedule: { cron: "0 9 * * *" },
        payload: {
          type: "systemEvent",
          text: "Check HEARTBEAT.md. Reply HEARTBEAT_OK if nothing needs attention.",
        },
      },
    ],
  },
}
```

---

## Security configuration

```json5
{
  security: {
    audit: {
      suppressions: [
        {
          checkId: "ssh-password-auth",
          titleIncludes: "SSH",
          reason: "Accepted: lab environment uses password auth",
        },
      ],
    },
    installPolicy: {
      enabled: false,
      targets: ["skill", "plugin"],
      exec: {
        source: "exec",
        command: "/usr/local/bin/openclaw-install-policy",
        args: [],
        timeoutMs: 30000,
        maxOutputBytes: 1048576,
        env: {},
        passEnv: [],
        trustedDirs: [],
      },
    },
  },
}
```

---

## Update configuration

```json5
{
  update: {
    channel: "stable",   // stable|extended-stable|beta|dev
    checkOnStart: true,  // npm installs only
    auto: {
      enabled: false,
    },
  },
}
```

---

## Session configuration

```json5
{
  session: {
    mainKey: "main",   // always "main" (any other value is ignored with warning)
    // Reset, maintenance, send-policy, thread-binding settings
  },
}
```

---

## UI preferences

```json5
{
  ui: {
    seamColor: "#76b900",
    assistant: {
      name: "OpenClaw",
      avatar: "🦞",
    },
    prefs: {
      theme: "claw",              // claw|knot|dash|custom
      themeMode: "dark",          // light|dark|system
      locale: "en",
      chatShowThinking: false,
      chatShowToolCalls: true,
      chatPersistCommentary: false,
      chatSendShortcut: "enter",  // enter|modifier-enter
      chatFollowUpMode: "steer",  // steer|queue
      sidebarEntries: [],
    },
  },
}
```

---

## Common validation rules

- Unknown root keys are rejected (except `$schema`, open-world channel/plugin sections)
- Type mismatches are rejected
- Enum values must match the schema exactly
- `session.mainKey` is always `"main"` — any other value triggers a warning

### Common errors

| Error | Fix |
|-------|-----|
| `Unknown key: channels.telegram.enablde` | Typo → `enabled` |
| `Type mismatch: expected boolean, got string` | Remove quotes for boolean/number |
| `Invalid enum: dmPolicy = "public"` | Valid: `"pairing" \| "allowlist" \| "open" \| "disabled"` |

---

## Provenance

- **Source files:** `src/config/types.openclaw.ts`, `src/config/types.agent-defaults.ts`, `src/config/types.telegram.ts`, `src/config/types.channels.ts`, `src/config/types.base.ts`, `src/config/defaults.ts`, `src/agents/defaults.ts`
- **Upstream commit:** `cd7b7f639da0d26424b52f3ffa2391f81acb5040`
- **OpenClaw version:** `2026.8.1`
- **Last validated:** 2026-08-10
