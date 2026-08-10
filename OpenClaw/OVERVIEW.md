# OpenClaw Overview

> **Current state:** OpenClaw v2026.8.1 · upstream `cd7b7f6`

OpenClaw is a multi-channel AI gateway with extensible messaging integrations. It connects LLM-powered agents to messaging platforms (Telegram, Discord, WhatsApp, Slack, Signal, IRC, and more) with a unified configuration, scheduling, and tool system.

## Core concepts

- **Gateway** — long-running daemon that manages channels, sessions, and agent dispatch
- **Agents** — configurable AI personalities with per-agent model, skills, and heartbeat
- **Channels** — messaging platform integrations (Telegram, Discord, WhatsApp, etc.)
- **Skills** — bundled capabilities (github, coding-agent, weather, tmux, etc.)
- **Cron** — scheduled jobs for periodic tasks, reminders, and automated workflows
- **Memory** — workspace-based memory files (`MEMORY.md`, `memory/YYYY-MM-DD.md`) for cross-session continuity
- **Sub-agents** — isolated sessions spawned for delegated work

## Architecture (high level)

```
┌─────────────────────────────────────────┐
│                Gateway                  │
│  ┌──────────┐  ┌──────────┐  ┌────────┐ │
│  │ Telegram │  │ Discord  │  │ Slack  │ │
│  │ Channel  │  │ Channel  │  │ Channel│ │
│  └────┬─────┘  └────┬─────┘  └───┬────┘ │
│       │              │            │       │
│  ┌────▼──────────────▼────────────▼────┐ │
│  │          Session / Agent             │ │
│  │  ┌─────────┐  ┌──────┐  ┌────────┐  │ │
│  │  │  Model  │  │ Tools│  │ Skills │  │ │
│  │  └─────────┘  └──────┘  └────────┘  │ │
│  └─────────────────────────────────────┘ │
│  ┌──────────┐  ┌──────────┐              │
│  │   Cron   │  │  Memory  │              │
│  └──────────┘  └──────────┘              │
└─────────────────────────────────────────┘
```

## Configuration

Config lives at `~/.openclaw/openclaw.json` (or `.jsonc`/`.yaml`). Key sections:

- `agents` — agent defaults, per-agent entries, bindings
- `channels` — channel sections (telegram, discord, whatsapp, etc.)
- `cron` — scheduled jobs
- `tools` — tool exposure and exec policy
- `models` — provider/model catalog

Run `openclaw config schema` for the full schema.

## CLI

```bash
openclaw gateway start          # start the daemon
openclaw gateway status         # check status
openclaw config schema          # print full config schema
openclaw config.schema.lookup agents.defaults.heartbeat  # lookup a field
openclaw channels status --probe  # check all channels
openclaw doctor                 # run diagnostics
openclaw logs --follow          # live logs
```

## Provenance

- **Source:** `package.json`, `README.md`, `src/` source tree
- **Upstream commit:** `cd7b7f639da0d26424b52f3ffa2391f81acb5040`
- **OpenClaw version:** `2026.8.1`
- **Last validated:** 2026-08-10
