# OpenClaw Configuration Guide

> Current as of 2026-08-19 (upstream `7a82d8b0f25`).

## Config Location

```
~/.openclaw/openclaw.json     # Main config (JSON5, atomically replaced on writes)
```

If missing, OpenClaw uses safe defaults. The config path must be a regular file (not a symlink). Use `OPENCLAW_CONFIG_PATH` to point at a non-default location.

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

## Top-Level Structure

```json5
{
  agents: { ... },       // Agent defaults and per-agent overrides
  channels: { ... },     // Channel connections
  session: { ... },      // Session management
  messages: { ... },     // Message routing, visible replies, group chat
  tools: { ... },        // Tool control, profiles, loop detection
  browser: { ... },      // Browser settings
  cron: { ... },         // Automation / scheduled jobs
  plugins: { ... },      // Extensions
  gateway: { ... },      // Gateway settings (port, bind, tailscale, controlUi)
  logging: { ... },      // Log level, file, retention
  commands: { ... },     // Owner allow-from, command access
}
```

## Strict Validation

- OpenClaw **only accepts** configurations that fully match the schema
- Unknown keys, malformed types, or invalid values → Gateway **refuses to start**
- Only `$schema` is exempt at root level
- `openclaw doctor` shows exact issues; `openclaw doctor --fix` applies auto-repairs
- Gateway keeps a last-known-good copy; `openclaw doctor --fix` restores it
- Rejected writes saved as `<path>.rejected.<timestamp>` for inspection
- Gateway blocks accidental clobber writes (dropping `gateway.mode`, losing `meta` block, shrinking >50%)

## Editing Config

```bash
# Interactive wizard
openclaw onboard
openclaw configure

# CLI one-liners
openclaw config get agents.defaults.workspace
openclaw config set agents.defaults.heartbeat.every "2h"
openclaw config unset plugins.entries.brave.config.webSearch.apiKey

# Control UI at http://127.0.0.1:18789 — Config tab
# Direct edit — Gateway watches file and hot-reloads
```

### Config Hot Reload

- Gateway watches `openclaw.json` and applies changes automatically
- Most config changes apply without restart
- Some changes (channel tokens, sandbox backend) require gateway restart
- Hot reload validates the new config; if invalid, current runtime keeps the last accepted config

---

## Agents

```json5
{
  agents: {
    defaults: {
      workspace: "~/.openclaw/workspace",
      model: {
        primary: "anthropic/claude-sonnet-4-6",
        fallbacks: ["openai/gpt-5.4"],
      },
      models: {
        "anthropic/claude-sonnet-4-6": { alias: "Sonnet" },
      },
      thinking: "low",            // low | medium | high
      imageModel: { primary: "openai/gpt-4.1-mini" },
      imageGenerationModel: { primary: "google/gemini-3-pro-image-preview" },
      imageMaxDimensionPx: 1200,  // image downscaling for vision-token savings
      heartbeat: {
        every: "30m",
        target: "owner",
        directPolicy: "allow",
      },
      sandbox: {
        mode: "off",              // off | non-main | all
      },
    },
    entries: {
      main: { default: true },
      work: {
        workspace: "~/workspaces/work",
        model: { primary: "anthropic/claude-opus-4-6" },
      },
    },
  },
}
```

### Key Agent Fields

| Field | Description |
|-------|-------------|
| `model.primary` | Primary model ref (`provider/model`) |
| `model.fallbacks` | Fallback model list |
| `models` | Per-model settings (aliases, etc.) |
| `modelPolicy.allow` | Explicit model allowlist for overrides (`provider/*` wildcards) |
| `thinking` | Reasoning effort: `low`, `medium`, `high` |
| `heartbeat` | Heartbeat config (see Heartbeat section) |
| `sandbox` | Sandbox config per agent |
| `skills` | Skill allowlist for agent |
| `userTimezone` | Timezone for activeHours and date/time |

---

## Heartbeat

```json5
{
  agents: {
    defaults: {
      heartbeat: {
        every: "30m",           // default: 30m; 1h for Anthropic OAuth; 0m disables
        target: "owner",        // owner | last | none | <channel id>
        directPolicy: "allow",  // allow | block — controls DM delivery
        lightContext: false,    // true: skip workspace bootstrap files
        isolatedSession: false, // true: fresh session each run (no history)
        model: "anthropic/claude-opus-4-6",  // optional model override
        prompt: "...",          // optional custom prompt (sent verbatim)
        activeHours: {
          start: "08:00",
          end: "22:00",
          timezone: "America/New_York",  // optional; uses userTimezone or host tz
        },
        timeoutSeconds: 600,    // optional; defaults to cadence capped at 600s
        accountId: "ops-bot",   // optional multi-account channel routing
        to: "12345678:topic:42", // optional Telegram topic/thread routing
      },
    },
  },
}
```

### Heartbeat Key Details

- **`directPolicy: "allow"`** (default): allow direct/DM heartbeat delivery
- **`directPolicy: "block"`**: suppress direct/DM delivery (`reason=dm-blocked`)
- **`target: "owner"`**: deliver to first resolvable operator DM from `commands.ownerAllowFrom`, then channel `allowFrom`; never resolves to a group
- **`target: "last"`**: follow most recent conversation (including groups)
- **`target: "none"`**: internal-only runs, no external delivery
- Scheduled heartbeats require `cron.enabled: true`; disabled cron = no scheduled heartbeats
- Heartbeat config is the desired-state input; the Automations scheduler owns the actual tick
- Per-agent `agents.entries.*.heartbeat` merges on top of defaults; if any agent has a `heartbeat` block, **only those agents** run heartbeats
- `isolatedSession: true` + `lightContext: true` = maximum token savings

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

### Streaming (Per-Channel)

Canonical key: `channels.<channel>.streaming` with nested `{ mode, ... }`.

| Mode | Behavior |
|------|----------|
| `off` | Disable preview streaming |
| `partial` | Single preview replaced with latest text |
| `block` | Preview updates in chunked/appended steps |
| `progress` | Progress/status preview, final answer at completion |

**Channel defaults:** Telegram=`progress`, Slack=`progress`, Discord=`off`, Mattermost=`partial`, MS Teams=`partial`.

Legacy keys rewritten by `openclaw doctor --fix` → `streaming.mode`.

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
    dmScope: "per-channel-peer",  // main | per-peer | per-channel-peer | per-account-channel-peer
    threadBindings: {
      enabled: true,
      idleHours: 24,
      maxAgeHours: 0,
    },
    reset: {
      mode: "daily",             // daily | manual | idle
      atHour: 4,
      idleMinutes: 120,
    },
    maintenance: {
      mode: "enforce",
      pruneAfter: "30d",
      maxEntries: 500,
    },
  },
}
```

---

## Tools

```json5
{
  tools: {
    profile: "full",            // minimal | coding | messaging | full (default)
    allow: [],
    deny: [],
    elevated: [],               // tools that bypass sandbox
    loopDetection: {
      enabled: true,
      warningThreshold: 10,
    },
    sandbox: {
      tools: [],                // plugin/MCP tools allowed in sandbox
    },
  },
}
```

---

## Gateway

```json5
{
  gateway: {
    port: 18789,
    mode: "local",              // local | remote
    bind: "loopback",           // loopback | all
    auth: {
      enabled: true,
      tokens: ["secret-token"],
    },
    tailscale: {
      mode: "serve",            // serve | funnel — for HTTPS URLs (Mini App, Control UI)
    },
    controlUi: {
      basePath: "/ui",          // optional path prefix
    },
  },
}
```

---

## Commands

```json5
{
  commands: {
    ownerAllowFrom: ["telegram:123456789"],  // who can run owner-only commands
  },
}
```

`commands.ownerAllowFrom` is critical for heartbeat delivery (`target: "owner"` resolves from this list).

---

## Cron / Automation

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

When `cron.enabled` is `false`: no scheduled heartbeats, no cron jobs run. Manual and event-driven wakes remain available.

---

## CLI Commands

```bash
openclaw config get agents.defaults.heartbeat.every
openclaw config set agents.defaults.heartbeat.every "2h"
openclaw config unset plugins.entries.brave.config.webSearch.apiKey
openclaw configure          # Interactive wizard
openclaw config schema      # Print canonical JSON Schema
openclaw doctor             # Diagnostics
openclaw doctor --fix       # Auto-repair
```
