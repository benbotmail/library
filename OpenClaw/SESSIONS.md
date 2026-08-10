# OpenClaw Sessions, Heartbeat & Cron Reference

> **Current state:** OpenClaw v2026.8.1 · upstream `cd7b7f6`

## Sessions

Sessions are the core conversation unit. Each session has a key (default: `"main"`) and contains the message history, agent assignment, and session state.

### Session keys

- `main` — the primary conversation session (always `"main"`)
- Custom keys for cron jobs, sub-agents, thread-bound sessions

### Session config

```json5
{
  session: {
    mainKey: "main",   // always "main"; other values are ignored with a warning
    // Reset, maintenance, send-policy, thread-binding settings
  },
}
```

### Sub-agent sessions

Spawned via `sessions_spawn` with their own isolated history:

```json5
{
  agents: {
    defaults: {
      subagents: {
        delegationMode: "suggest",    // suggest | prefer
        allowAgents: [],              // target agent ids (* = any)
        maxConcurrent: 8,
        maxSpawnDepth: 1,            // nesting depth
        maxChildrenPerAgent: 5,
        archiveAfterMinutes: 60,
        runTimeoutSeconds: 0,        // 0 = no timeout
      },
    },
  },
}
```

---

## Heartbeat

Heartbeat runs are periodic background agent turns that can check emails, calendars, and perform proactive work.

### Config

```json5
{
  agents: {
    defaults: {
      heartbeat: {
        agentId: undefined,           // agent that owns heartbeat runs
        every: "30m",                 // interval (duration string; default unit: minutes)
        activeHours: {
          start: undefined,           // "09:00" (24h, local time, inclusive)
          end: undefined,             // "23:00" (exclusive; "24:00" = end-of-day)
          timezone: "user",           // "user" | "local" | IANA TZ id
        },
        model: undefined,             // heartbeat model override (provider/model)
        session: undefined,           // session key ("main" or explicit)
        target: "last",               // "last" | "none" | channel id
        directPolicy: "allow",        // "allow" | "block"
        to: undefined,                // destination override
        accountId: undefined,         // multi-account channel
        prompt: undefined,            // override heartbeat prompt body
        timeoutSeconds: undefined,    // run timeout
        lightContext: false,          // skip workspace bootstrap files
        isolatedSession: false,       // run without conversation history (saves tokens)
      },
    },
    // Per-agent override
    entries: [
      {
        id: "codex",
        heartbeat: {
          every: "15m",
          directPolicy: "block",      // block DM delivery for this agent's heartbeat
          isolatedSession: true,
        },
      },
    ],
  },
}
```

### `directPolicy` behavior

| Value | Effect |
|-------|--------|
| `"allow"` (default) | Heartbeat output can be delivered to DMs |
| `"block"` | Heartbeat output is NOT delivered to direct/DM chats; only group/channel targets receive it |

The `directPolicy` is checked in `src/infra/outbound/targets.ts` for delivery routing.

### `lightContext` vs `isolatedSession`

| Mode | Effect |
|------|--------|
| `lightContext: true` | Skips workspace bootstrap files; monitor scratch is still injected |
| `isolatedSession: true` | Runs in a fresh session with no prior conversation history — dramatically reduces per-heartbeat token cost |

---

## Cron

Cron jobs are scheduled tasks that run agent turns or system events on a fixed schedule.

### Config

```json5
{
  cron: {
    enabled: true,
    triggers: { enabled: true },
    webhookToken: "secret",         // bearer token for webhook delivery
    webhookSsrfPolicy: { /* SSRF controls */ },
    sessionRetention: "24h",        // duration string | false
    failureAlert: {
      enabled: false,
      after: 2,                     // alert after N failures
      cooldownMs: 3600000,
      includeSkipped: false,
      mode: "announce",             // announce | webhook
      channel: undefined,
      to: undefined,
      accountId: undefined,
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
      {
        name: "project-status",
        enabled: true,
        schedule: { cron: "0 10 * * 1" },
        payload: {
          type: "agentTurn",
          message: "Weekly project check-in. Review git status and pending issues.",
        },
      },
    ],
  },
}
```

### Cron delivery

Cron job output is delivered via the outbound delivery system. The `delivery` config on a cron job controls:

- `channel` — target channel id
- `to` — destination (chat id, user id, etc.)
- `accountId` — multi-account support
- `mode` — `"announce"` or `"webhook"`

### Session retention

Completed cron run sessions are pruned after `sessionRetention` (default: `"24h"`). Set to `false` or `"0h"` to disable pruning.

---

## Provenance

- **Source files:** `src/config/types.agent-defaults.ts`, `src/config/types.cron.ts`, `src/infra/outbound/targets.ts`, `src/cron/service.ts`, `src/cron/delivery.ts`
- **Upstream commit:** `cd7b7f639da0d26424b52f3ffa2391f81acb5040`
- **OpenClaw version:** `2026.8.1`
- **Last validated:** 2026-08-10
