# Concepts and Terminology

> Core concepts and their relationships

## Glossary

| Term | Definition |
|------|-----------|
| **Gateway** | The central Node.js daemon process that manages all OpenClaw state, routing, and tool execution. |
| **Agent** | An AI identity with its own workspace, model config, and session context. Default agent ID is `main`. |
| **Session** | A conversation context with message history. Identified by key like `agent:main:main`. |
| **Main Session** | The primary DM conversation context for an agent. Key pattern: `agent:<agentId>:main`. |
| **Channel** | A messaging surface plugin (Telegram, Discord, WhatsApp, etc.). Routes replies deterministically. |
| **Node** | A paired device (iOS/Android/macOS) providing camera, mic, canvas, and device actions. |
| **Tool** | A capability exposed to the agent (exec, read, write, web_search, browser, etc.). |
| **Skill** | A bundled set of instructions + tools with a `SKILL.md` that guides when/how to use them. |
| **Plugin** | An extension package adding channels, tools, or providers. |
| **Heartbeat** | Periodic agent turns in the main session for proactive checks. Configured under `agents.defaults.heartbeat`. |
| **Cron Job** | A scheduled task that runs in an isolated session at specified times. |
| **Sandbox** | Isolation layer for exec/tool operations (Docker-based or policy-based). |
| **Pairing** | One-time approval flow for unknown senders to gain DM access. |
| **DM Scope** | Session routing mode for direct messages: `main` (shared) or `per-channel-peer` (isolated). |
| **Workspace** | The agent's working directory (`~/.openclaw/workspace` by default) with bootstrap files. |
| **Bootstrap Files** | Auto-created workspace files: `AGENTS.md`, `SOUL.md`, `IDENTITY.md`, `USER.md`, `BOOTSTRAP.md`. |
| **Context Injection** | How workspace files are injected into the system prompt. Modes: `always` (default), `continuation-skip`, `never`. |

## Relationships

```
Gateway
├── Agents (agents.defaults + agents.entries.*)
│   ├── Workspace (agents.defaults.workspace)
│   │   ├── Bootstrap Files (AGENTS.md, SOUL.md, USER.md, etc.)
│   │   ├── MEMORY.md
│   │   └── memory/*.md
│   ├── Sessions (session.dmScope)
│   │   ├── Main Session
│   │   ├── Isolated Sessions
│   │   └── Cron Sessions
│   └── Model Config (agents.entries.*.model)
├── Channels (channels.*)
│   ├── DM Policy (dmPolicy)
│   ├── Group Policy (groupPolicy)
│   └── Streaming Config
├── Tools (tools.*)
│   ├── Tool Profiles (minimal, coding, messaging, full)
│   ├── Tool Groups (group:fs, group:runtime, etc.)
│   ├── Allow/Deny (tools.allow, tools.deny)
│   └── Elevated (tools.elevated)
├── Skills (skills.*)
└── Plugins (plugins.*)
```

## Session Types

| Session Type | Key Pattern | Description |
|-------------|-------------|-------------|
| Main | `agent:<agentId>:main` | Primary DM conversation, shared across channels by default |
| Per-channel-peer | `agent:<agentId>:<channelId>:<peerId>` | Isolated per channel+peer when `dmScope: "per-channel-peer"` |
| Cron | `agent:<agentId>:cron:<jobId>` | Ephemeral session for scheduled jobs |
| Sub-agent | `agent:<agentId>:subagent:<uuid>` | Spawned by main agent for delegated tasks |

## DM Policy Values

| Policy | Behavior |
|--------|----------|
| `pairing` (default) | Unknown senders get a one-time pairing code; owner approves via CLI |
| `allowlist` | Only senders in `allowFrom` list |
| `open` | Allow all inbound DMs (requires `allowFrom: ["*"]`) |
| `disabled` | Ignore all inbound DMs |

## Group Policy Values

| Policy | Behavior |
|--------|----------|
| `allowlist` (default) | Only groups matching configured allowlist |
| `open` | Bypass group allowlists (mention-gating still applies) |
| `disabled` | Block all group/room messages |

## Tool Profiles

| Profile | Description |
|---------|-------------|
| `minimal` | `session_status` only |
| `coding` | File system, runtime, web, sessions, memory, cron, goals, skills, media |
| `messaging` | Messaging tools, sessions, sub-agents, session_status, ask_user |
| `full` | No restriction |

## Reload Modes

| Mode | Behavior |
|------|----------|
| `hybrid` (current) | Hot-apply where possible, restart Gateway when needed |
| `hot` (retired) | Mapped to `hybrid` by `openclaw doctor --fix` |
| `restart` (retired) | Mapped to `hybrid` by `openclaw doctor --fix` |

## Heartbeat

Periodic main-session turns for proactive monitoring. Configured via `agents.defaults.heartbeat`:
- `every`: duration string (`30m`, `2h`, `0m` to disable). Default: `30m`
- `target`: `owner` | `last` | `none` | `<channel-id>`
- `directPolicy`: `allow` (default) or `block` for DM-style heartbeat targets

## Cron

Scheduled isolated-session tasks. Configured via `cron` section:
- `cron.enabled`: must be `true` (or `OPENCLAW_SKIP_CRON=1` to disable)
- `sessionRetention`: prune completed sessions (default `"24h"`)
- Jobs managed via `openclaw automations` CLI
- `--delete-after-run` for one-shot jobs
- `--timeout-seconds` per-job override
- Default timeout: `agents.defaults.timeoutSeconds` (48h default)
- Scheduler watchdog kills stale jobs after 60 minutes
- Command jobs default to 10-minute timeout
