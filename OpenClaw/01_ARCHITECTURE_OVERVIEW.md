# OpenClaw Architecture Overview

> System architecture, components, and data flows

## What OpenClaw Is

OpenClaw is a **self-hosted gateway** that connects chat applications (WhatsApp, Telegram, Discord, Slack, iMessage, Signal, Matrix, and more) to AI agents. A single Gateway process manages sessions, routes messages, executes tools, and maintains conversation state.

## High-Level Architecture

```
Chat Apps (WhatsApp / Telegram / Discord / Slack / Signal / iMessage / ...)
                    │
                    ▼
            ┌───────────────┐
            │   Gateway     │  ← Control plane (ws://127.0.0.1:18789)
            │  (Node.js)    │     JSON5 config: ~/.openclaw/openclaw.json
            └───────┬───────┘
                    │
     ┌──────────────┼──────────────┐
     │              │              │
     ▼              ▼              ▼
  AI Providers   CLI / WebUI    Mobile Nodes
  (OpenAI,      (openclaw ...)  (iOS/Android/macOS)
   Anthropic,                     Camera, mic, canvas
   Google, etc.)
```

## Core Components

### Gateway
The central daemon process. Responsibilities:
- Reads config from `~/.openclaw/openclaw.json` (JSON5)
- Manages agent sessions and conversation state
- Routes inbound messages from channels to agents
- Executes tool calls and returns results
- Handles hot-reload of configuration changes
- Listens on `ws://127.0.0.1:18789` by default

### Agents
An agent is an AI identity with its own workspace, system prompt, model config, and session context. The default agent is `main`. Multi-agent fleets use `agents.entries.*` with per-agent overrides.

Key agent properties:
- **Workspace**: `~/.openclaw/workspace` (default), with bootstrap files (`AGENTS.md`, `SOUL.md`, `USER.md`, etc.)
- **Model**: configurable per-agent via `agents.entries.*.model`
- **Session scope**: DM conversations converge on the agent's main session by default

### Sessions
A session is a conversation context with its own message history. Types:
- **Main session**: direct chat context, key pattern `agent:<agentId>:main`
- **Isolated sessions**: per-channel or per-conversation when `session.dmScope` is `per-channel-peer`
- **Cron sessions**: ephemeral sessions for scheduled jobs
- **Sub-agent sessions**: spawned by the main agent for delegated tasks

Session DM scope values:
- `main` (default) — all DMs across channels share one main session
- `per-channel-peer` — each channel+peer gets its own session

### Channels
Channel plugins connect external messaging platforms. Each channel:
- Starts automatically when its config section exists (unless `enabled: false`)
- Has DM and group access policies
- Routes replies back to the originating channel deterministically
- Telegram ships in core; others install as plugins

### Nodes
Paired devices (iOS, Android, macOS) that extend the Gateway with:
- Camera and microphone access
- Canvas rendering
- Device actions (notifications, sensors)

### Tools
Built-in capabilities exposed to the AI agent:
- `exec` / `process` — shell command execution
- `read` / `write` / `edit` — file operations
- `web_search` / `web_fetch` — web access
- `browser` — browser automation
- `message` — channel messaging
- `sessions_spawn` / `sessions_yield` — sub-agent delegation
- `memory_search` / `memory_get` — memory retrieval
- `cron` — scheduled jobs
- `image` — image analysis
- `tts` — text-to-speech

### Skills
Bundled capabilities with their own `SKILL.md` instructions. Skills are discovered and loaded on-demand. Each skill defines triggers, tools, and workflow guidance.

### Plugins
Extension packages providing additional channels, tools, or providers. Installed via `openclaw plugins install <spec>`.

## Configuration Layers

```
~/.openclaw/openclaw.json          ← Main config (JSON5)
~/.openclaw/credentials/            ← Auth credentials
~/.openclaw/workspace/              ← Agent workspace
  ├── AGENTS.md                     ← Agent behavior instructions
  ├── SOUL.md                       ← Agent personality
  ├── MEMORY.md                     ← Long-term memory
  └── memory/YYYY-MM-DD.md          ← Daily notes
```

### Config two-bucket rule
- **Root-level siblings**: infrastructure and cross-agent defaults (`channels.*`, `gateway.*`, `tools.*`, `session.*`, etc.)
- **`agents.defaults`**: agent-loop behavior defaults (model, workspace, heartbeat, etc.)
- **`agents.entries.<id>`**: per-agent overrides that replace (not merge) defaults

## Hot Reload

The Gateway watches `~/.openclaw/openclaw.json` and applies changes automatically. Current reload mode is **hybrid** (hot-apply where possible, restart when needed). The earlier `hot` and `restart` modes are retired; `openclaw doctor --fix` maps both to `hybrid`.

Reload debounce is no longer configurable and runs behind a built-in default.

## Data Flow: Inbound Message

1. User sends message on a channel (e.g., Telegram)
2. Channel plugin receives message, checks DM/group policy
3. Gateway resolves session (main or per-channel based on `session.dmScope`)
4. Agent system prompt is assembled (workspace files + context injection)
5. Provider API call (OpenAI, Anthropic, Google, etc.)
6. Tool calls executed if requested (with sandbox/policy checks)
7. Response routed back to originating channel
8. Session history updated

## Multi-Agent Routing

When `agents.ownership: "explicit"` is set (stamped by multi-agent fleet setup):
- Channels and ambient services need explicit bindings
- No implicit default agent
- `agents.entries.*` defines each agent with its own config
- Per-agent overrides do not merge with defaults (they replace)
