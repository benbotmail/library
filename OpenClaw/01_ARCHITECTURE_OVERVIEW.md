# OpenClaw Architecture Overview

OpenClaw is a **self-hosted multi-channel gateway** for AI agents. The Gateway is a single long-running process that connects chat applications (WhatsApp, Telegram, Discord, etc.) to agent runtimes, managing sessions, routing, tools, and state.

## High-level components

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              Chat Apps                                    │
│  WhatsApp │ Telegram │ Discord │ iMessage │ Signal │ Slack │ WebChat     │
└──────────────────────┬──────────────────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            Gateway                                      │
│  ┌──────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐              │
│  │ Channel  │  │ Routing   │  │ Session  │  │ Plugins  │              │
│  │ Handlers │  │ Engine    │  │ Manager  │  │ System   │              │
│  └────┬─────┘  └─────┬─────┘  └─────┬─────┘  └─────┬─────┘              │
│       │              │              │              │                     │
│       └──────────────┼──────────────┼──────────────┘                     │
│                      ▼              ▼                                  │
│              ┌──────────────┐  ┌───────────┐                           │
│              │ Context     │  │ Tool      │                           │
│              │ Engine     │  │ Executor  │                           │
│              └──────────────┘  └─────┬─────┘                           │
└──────────────────────────────────────┼───────────────────────────────────┘
                                       │
                    ┌──────────────────┼──────────────────┐
                    ▼                  ▼                  ▼
              ┌──────────┐      ┌──────────┐      ┌──────────┐
              │  Agent   │      │ Browser  │      │  Nodes   │
              │ Runtime  │      │ Control  │      │ (Mobile) │
              └──────────┘      └──────────┘      └──────────┘
```

## Gateway responsibilities

### 1. Channel management
- Maintains persistent connections to configured chat platforms
- Handles inbound messages (DMs, group messages, media, reactions)
- Formats and sends outbound messages (text, media, reactions)
- Normalizes platform-specific quirks into a unified inbound model

**Source:** `src/channels/`, `src/plugins/providers/`

### 2. Routing engine
- Routes inbound messages to agents based on:
  - Channel configuration (`dmPolicy`, `groupPolicy`)
  - Per-channel routing rules
  - Slash commands (`/model`, `/reasoning`, etc.)
  - Mention patterns in group chats
- Supports multi-agent routing with isolation

**Source:** `src/routing/`, `src/commands/`

### 3. Session management
- Creates isolated sessions per:
  - Sender (default: per-phone-number or per-user-id)
  - Agent ID (when using multi-agent routing)
  - Workspace (for context isolation)
- Maintains conversation history and context
- Handles sub-agent spawning and cleanup
- Persists sessions across restarts

**Source:** `src/sessions/`, `src/agents/`

### 4. Context engine
- Injects workspace context into agent turns
- Loads `MEMORY.md`, `USER.md`, `SOUL.md` (main session only)
- Provides project context, file lists, and recent history
- Manages token budgets for context windows

**Source:** `src/context-engine/`, `src/memory/`

### 5. Tool execution
- Executes tools on behalf of agents (exec, browser, web_search, etc.)
- Enforces permission checks and approval workflows
- Handles sandboxing (isolated shells, filesystem restrictions)
- Manages tool timeouts and cancellations

**Source:** `src/process/`, `src/browser/`, `src/web-search/`

### 6. Plugin system
- Dynamically loads plugins (channels, providers, tools)
- Extends core capabilities (web search, GitHub, weather, etc.)
- Provides SDK for custom plugin development
- Validates plugin manifests and config schemas

**Source:** `src/plugins/`, `src/plugin-sdk/`

### 7. Configuration management
- Reads and validates `~/.openclaw/openclaw.json` (JSON5)
- Applies config changes via hot-reload (file watcher)
- Provides CLI and UI for config management
- Maintains config schema and validation rules

**Source:** `src/config/`, `src/daemon/`

## Agent runtimes

### Main sessions
- Direct conversations with the user
- Full workspace access and tool permissions
- Can spawn sub-agents for isolated tasks
- Default model: `agents.defaults.model.primary`

### Sub-agents
- Isolated agent sessions spawned by `sessions_spawn`
- Inherit parent workspace by default
- Independent session lifecycle and cleanup
- Useful for: coding tasks, batch operations, parallel processing

### ACP harnesses
- Agent Control Protocol wrappers for external agents
- Supports Codex, Claude Code, Pi, and custom agents
- Thread-bound or one-shot execution modes
- Used by `coding-agent` skill and `/codex` commands

**Source:** `src/agents/`, `src/acp/`

## Node pairing

Mobile nodes (iOS, Android) can be paired to the Gateway for:

- **Canvas rendering** — Live UI canvases sent from agents
- **Camera access** — Take photos/videos on-demand
- **Device actions** — Send notifications, control media, get location
- **Voice interactions** — Speech-to-text and text-to-speech

Nodes connect via WebRTC through a signaling server (gateway or tailnet).

**Source:** `src/node-host/`, `src/canvas-host/`

## Data flow: inbound message

```
1. Chat platform → Gateway channel handler
2. Channel handler → Normalize inbound metadata
3. Routing engine → Check dmPolicy/groupPolicy
4. Routing engine → Select agent (default or per-rule)
5. Session manager → Get/create session
6. Context engine → Inject workspace context
7. Agent runtime → Generate response + tool calls
8. Tool executor → Run tools (exec, browser, etc.)
9. Agent runtime → Final response
10. Channel handler → Format and send to chat platform
```

## Data flow: tool execution

```
1. Agent calls tool with parameters
2. Tool executor → Validate permissions
3. If elevated → Request user approval
4. Tool executor → Run tool (sandboxed if configured)
5. Tool → Return result to agent
6. Agent → Continue reasoning with tool result
```

## Key design principles

### Single source of truth
- The Gateway is the authoritative session store
- Channels are stateless message passers
- Agents are ephemeral per-turn executors
- Nodes are peripheral devices, not coordinators

### Isolation boundaries
- Sessions are isolated by sender, agent, and workspace
- Plugins run in their own process contexts
- Tools run with scoped permissions and sandboxing
- Config validation prevents misconfiguration at boot

### Extensibility
- Plugins add channels, providers, and tools
- Skills provide specialized workflows and conditional logic
- ACP harnesses support arbitrary agent runtimes
- Config schema is extensible via plugin manifests

### Self-hosted first
- Runs on your hardware, your rules
- No external services required (optional: Tailscale for nodes)
- All data stays on your machine
- Open source and MIT licensed

## Deployment modes

### Single-user workstation
- Gateway runs as user systemd service or launchd
- Workspace at `~/.openclaw/workspace`
- Config at `~/.openclaw/openclaw.json`
- Direct access to local files and tools

### VPS / remote server
- Gateway runs on Linux VPS
- Remote access via SSH or Tailscale
- Workspace and config on remote filesystem
- Optional local node for mobile/canvas features

### Docker container
- Gateway runs in containerized environment
- Volume mounts for workspace and config
- Port mapping for remote access
- Useful for reproducible deployments

## Performance considerations

### Latency
- Local execution: minimal network latency
- Tool calls: depend on external APIs (OpenAI, browser, etc.)
- Channel APIs: vary by platform (Telegram fast, WhatsApp slower)
- Agent generation: dominated by model provider latency

### Scalability
- Single Gateway process handles multiple channels and sessions
- Sub-agents enable parallel task execution
- No built-in multi-node clustering (use load balancer if needed)
- Tool execution is synchronous (one tool at a time per session)

### Resource usage
- Memory: ~100-300 MB base + per-session context
- CPU: light when idle, spikes during agent inference
- Disk: workspace grows with sessions, memory files, logs
- Network: depends on model providers and channel APIs

## Security model

### Authentication
- Channel authentication (API tokens, OAuth, QR codes)
- Admin access via Control UI (requires token or local access)
- Node pairing via one-time codes or Tailscale identity

### Authorization
- `dmPolicy` and `groupPolicy` control inbound access
- Tool permissions per session (configurable)
- Elevated commands require user approval
- Filesystem access scoped to workspace

### Data isolation
- Sessions isolated by sender and workspace
- MEMORY.md only in main sessions (not groups)
- Config validation prevents cross-session leakage
- Secrets managed via `openclaw secret` commands

## Provenance
- **Source:** `src/gateway/`, `src/daemon/`, README.md, docs/index.md
- **Last validated:** 2026-03-18 (against openclaw@latest from GitHub)
