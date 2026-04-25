# Gateway Components

## Scope
Gateway architecture overview: entry points, routing engine, session management, and plugins. Describes how requests flow through the Gateway and interact with core components.

## Audience
- Operators deploying and maintaining OpenClaw Gateways
- Developers extending Gateway functionality via plugins
- LLM systems retrieving OpenClaw architecture context

## Architecture Overview

The Gateway is a central daemon that receives messages from channels, routes them to agents, manages tool execution, and handles persistence. All messaging flows through the Gateway as the single coordination point.

## Core Components

### 1. Entry Points

Message entry into the Gateway occurs through multiple concurrent channels:

| Entry Point | Protocol | Purpose |
|-------------|----------|---------|
| **Channel plugins** | Platform APIs (WebSocket, webhook, polling) | Inbound messages from user-facing platforms (WhatsApp, Telegram, Discord, Signal, etc.) |
| **HTTP API** | REST/JSON | Programmatic control, health checks, manual message injection |
| **CLI commands** | Local process execution | Direct Gateway control (`openclaw gateway start/stop/status`, config management) |
| **Cron scheduler** | Internal timer | Scheduled payloads and time-based automation |
| **Webhook receivers** | HTTP POST | External system integration and event-driven triggers |

All entry points normalize messages into a unified internal representation before routing.

### 2. Routing Engine

The routing engine determines how messages flow to the correct agent and back:

**Routing Decision Tree:**
```
Incoming Message
    ↓
[Identify Session]
    ↓
[Session exists?] → No → [Create new session] → [Assign agent]
    ↓ Yes
[Agent assigned?] → No → [Route to default agent]
    ↓ Yes
[Message type?] → [Apply routing rules (per-agent, channel policies, mention patterns)]
    ↓
[Deliver to agent execution context]
```

**Routing Rules:**
- **Per-agent sessions:** Some agents get dedicated sessions (identified via agentId or session labels)
- **Channel policies:** `dmPolicy` and `groupPolicy` control session routing in DMs vs groups
- **Mention routing:** In group chats, messages mentioning the Gateway or configured keywords are routed
- **Multi-agent patterns:** Sub-agent spawning and escalation via `sessions_spawn`
- **Load balancing:** Distribution across available model instances when configured

### 3. Session Model

Sessions are the fundamental isolation and persistence unit in OpenClaw:

**Session Lifecycle:**
```
[Creation] → [Active Turn] → [Tool Execution] → [Response Generation] → [Persistence] → [Next Turn or Cleanup]
```

**Session Types:**
| Type | Description | Lifetime |
|------|-------------|----------|
| **Main session** | Primary session with the human (DM or designated thread) | Persistent until explicitly closed or timeout |
| **Sub-agent session** | Spawned via `sessions_spawn` for isolated work | One-shot (`run`) or persistent (`session`) |
| **ACP harness session** | Coding sessions (Codex, Claude Code, Pi) via ACP runtime | Thread-bound or ephemeral |
| **Cron-triggered session** | Scheduled task execution | Ephemeral, payload-delivery mode |

**Session State:**
- **Isolation:** Each session has its own workspace context and message history
- **Persistence:** Messages and tool results stored for conversation continuity
- **Sub-agent orchestration:** Main sessions can spawn and manage sub-agents
- **Cleanup:** Timed-out sessions are reclaimed; explicit cleanup via `subagents kill`

### 4. Agent Execution Context

Agents receive routed messages and generate responses:

**Agent Runtime:**
- **Model selection:** Resolved via config aliases, per-session overrides, or default model
- **Tool access:** Filtered by agent permissions, tool allowlists, and approval requirements
- **Thinking modes:** Configurable reasoning levels (off, low, high) via `thinking` or `/reasoning`
- **Context window:** Limited by model;MEMORY.md and workspace files loaded selectively
- **Timeout enforcement:** `runTimeoutSeconds` and `timeoutSeconds` prevent runaway execution

**ACP Harness (Coding Sessions):**
- **Runtime:** `acp` (vs `subagent` for general tasks)
- **AgentId:** Required unless `acp.defaultAgent` configured
- **Thread binding:** Persistent thread-bound sessions for Discord flows
- **Workspace inheritance:** Sub-agents inherit parent workspace directory

### 5. Tool Execution

The Gateway mediates all tool execution with security boundaries:

**Tool Execution Flow:**
```
Agent requests tool
    ↓
[Check tool availability] → [Tool not allowed] → [Reject or escalate]
    ↓ Allowed
[Check approval requirements] → [Requires approval] → [Request approval via /approve]
    ↓ Approved or not required
[Execute tool with appropriate permissions]
    ↓
[Return result to agent context]
```

**Security Layers:**
- **Sandboxing:** Some tools run in containerized environments when `sandbox=require`
- **Approvals:** Elevated commands (exec with permissions, system access) require explicit approval
- **Permission modes:** `bypassPermissions` for Claude Code; other agents use approval flow
- **Dangerous pattern detection:** Warnings for destructive operations (rm, system calls, etc.)

### 6. Plugin System

Plugins extend Gateway functionality without core modifications:

**Plugin Types:**
| Type | Purpose | Examples |
|------|---------|----------|
| **Provider plugins** | Add LLM providers, models, and capabilities | OpenAI, Anthropic, local models |
| **Channel plugins** | Add messaging platforms | WhatsApp, Telegram, Discord, Signal |
| **Tool plugins** | Add specialized tools | GitHub, Brave Search, weather, coding-agent |
| **Skill plugins** | Conditional logic and behavior injection | healthcheck, node-connect, video-frames |

**Plugin Discovery:**
- Configuration via `plugins.entries` in config
- Auto-discovery from configured plugin directories
- Skills loaded from `~/.npm-global/lib/node_modules/openclaw/skills/`

## Data Flow Example

```
User sends message on WhatsApp
    ↓
WhatsApp channel plugin receives message
    ↓
Gateway normalizes message (metadata, context, attachments)
    ↓
Routing engine identifies session (existing or new)
    ↓
Session assigned to appropriate agent
    ↓
Agent executes with model + tools + context
    ↓
Agent requests tool (e.g., web_search)
    ↓
Gateway checks tool permissions and approval
    ↓
Tool executed, result returned to agent
    ↓
Agent generates response
    ↓
Response routed back via WhatsApp channel plugin
    ↓
Message stored in session history
```

## Configuration Components

Key config sections related to Gateway components:

| Component | Config Section | Example |
|-----------|----------------|---------|
| Channels | `plugins.entries.<channel>` | `plugins.entries.telegram = { token, botId }` |
| Agents | `agents.defaults` | `agents.defaults.model = "zai/glm-5"` |
| Routing | `sessions` | `sessions.defaults.label` |
| Tools | `tools` | `tools.approval.required = true` |
| Plugins | `plugins` | `plugins.paths = [...]` |
| Security | `security` | `security.allowlists.senders = [...]` |

## Hot Reload Behavior

The Gateway supports hot reloading for most config changes:

**Hot Reloadable:**
- Agent configuration (model, thinking, permissions)
- Channel credentials and settings
- Routing rules and policies
- Plugin configuration (non-structural)

**Requires Restart:**
- New plugin paths or plugin directory changes
- Gateway listener ports
- Security mode changes
- Database or persistence backend changes

Hot reload is triggered via `openclaw gateway restart` or by sending SIGHUP to the Gateway process.

## Monitoring and Diagnostics

**Health Checks:**
- `openclaw status` — Gateway daemon status, uptime, session counts
- `openclaw health` — Component health, plugin status, connectivity checks
- `openclaw doctor` — Diagnostic data collection for troubleshooting

**Logging:**
- Gateway logs in `/var/log/openclaw/gateway.log` (systemd) or `~/.openclaw/logs/` (local)
- Session logs for debugging agent turns
- Channel-specific logs for delivery issues

**Metrics:**
- Session creation and cleanup rates
- Tool execution success/failure
- Channel message throughput
- Model response latency (when available from provider)

## Common Patterns

**DM vs Group Behavior:**
- DMs typically route directly to the default agent
- Groups require mention routing or `groupPolicy: open` to avoid noise
- Per-channel policies override defaults

**Sub-agent Orchestration:**
- Main agents spawn sub-agents for isolation and parallel work
- `subagents list` to check running sub-agents
- `subagents steer` to guide ongoing work without spawning new sessions

**ACP Harness Thread Flows:**
- Discord thread-bound sessions: `runtime: "acp"`, `thread: true`, `mode: "session"`
- Useful for persistent coding workflows with conversation continuity

## Related Documentation

- `21_SESSION_MODEL.md` — Detailed session lifecycle and persistence
- `22_ROUTING_AND_DISPATCH.md` — Request routing rules and multi-agent patterns
- `23_CONFIGURATION_SCHEMA_REFERENCE.md` — Core config schema sections
- `50_TOOL_SYSTEM_OVERVIEW.md` — Tool execution model and security
- `60_PLUGIN_ARCHITECTURE.md` — Plugin system and development

## Troubleshooting

**Gateway won't start:** Check port conflicts, log files in `/var/log/openclaw/` or `~/.openclaw/logs/`

**Messages not routing:** Verify channel credentials, check `dmPolicy`/`groupPolicy`, review Gateway logs for routing errors

**Session loss after restart:** Ensure session persistence is enabled, check disk permissions for session storage

**Plugin not loading:** Verify plugin configuration, check `plugins.paths`, review Gateway logs for plugin errors

## Provenance

- Gateway architecture from OpenClaw source: `packages/gateway/`
- Plugin system from `packages/plugins/` and `packages/skills/`
- Session model from `packages/sessions/` and `packages/subagents/`
- Routing logic from `packages/gateway/src/router.ts`
- Configuration schema from `packages/config/`
- Official docs: <https://docs.openclaw.ai>
- Repository: <https://github.com/openclaw/openclaw>
