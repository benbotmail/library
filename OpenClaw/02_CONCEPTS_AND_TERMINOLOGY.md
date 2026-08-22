# Concepts and Terminology

OpenClaw uses specific terminology that may differ from other chatbot platforms. This reference defines core concepts and their relationships.

## Core entities

### Gateway
The **Gateway** is a single long-running process that:
- Manages connections to chat channels (WhatsApp, Telegram, Discord, etc.)
- Routes messages to agents and responses back to channels
- Maintains session state, configuration, and plugins
- Executes tools on behalf of agents

**Physical location:** Usually runs on your workstation or a VPS
**Config:** `~/.openclaw/openclaw.json`
**Workspace:** `~/.openclaw/workspace`

### Agent
An **agent** is an AI model instance that processes messages and generates responses. Agents have:
- A model ID (e.g., `anthropic/claude-sonnet-4-5`)
- A tool set (exec, browser, web_search, etc.)
- A session context (conversation history, workspace)
- Thinking/reasoning settings

**Types:**
- **Main session agent:** Direct conversation with a user
- **Sub-agent:** Isolated agent spawned by `sessions_spawn`
- **ACP harness:** Wrapper for external agents (Codex, Claude Code, Pi)

### Session
A **session** is an isolated conversation context tied to:
- A **sender** (phone number, user ID, etc.)
- An **agent** (default or specific agent ID)
- A **workspace** (shared context directory)

**Session isolation:**
- Each sender gets their own session by default
- Sessions maintain conversation history and context
- MEMORY.md is only available in main sessions (not groups)
- Sub-agents inherit parent workspace but have independent sessions

### Node
A **node** is a paired mobile device (iOS or Android) that provides:
- **Canvas rendering** — Live UI canvases from agents
- **Camera access** — Take photos/videos on request
- **Device actions** — Notifications, media control, location
- **Voice I/O** — Speech-to-text and text-to-speech

**Connection:** WebRTC through Gateway signaling (direct or Tailscale)

### Plugin
A **plugin** extends Gateway capabilities. Plugin types:
- **Channel plugins** — Add new chat platforms (e.g., Mattermost)
- **Provider plugins** — Add new LLM providers or tools
- **Skill plugins** — Add specialized workflows (e.g., gh-issues, weather)

**Location:** `~/.npm-global/lib/node_modules/openclaw-skills-*` or user paths

### Skill
A **skill** is a specialized workflow defined in `SKILL.md` files. Skills:
- Provide step-by-step guidance for tasks (e.g., coding-agent, github)
- Include conditional logic (when to trigger the skill)
- Define tool permissions and conventions
- Are discovered automatically by the agent

**Location:** `~/.npm-global/lib/node_modules/openclaw/skills/*`

## Configuration concepts

### dmPolicy
Controls who can send DMs to the Gateway on a channel.

**Values:**
- `"pairing"` (default) — Unknown senders get a one-time pairing code
- `"allowlist"` — Only senders in `allowFrom` or paired store can message
- `"open"` — Allow all inbound DMs (requires `allowFrom: ["*"]`)
- `"disabled"` — Ignore all DMs

**Example:**
```json5
{
  channels: {
    telegram: {
      dmPolicy: "allowlist",
      allowFrom: ["tg:123456789"],
    },
  },
}
```

### groupPolicy
Controls how Gateway responds to group messages.

**Values:**
- `"requireMention"` — Only respond when mentioned
- `"allowlist"` — Only respond in listed groups
- `"open"` — Respond to all messages (rare)
- `"disabled"` — Never respond to groups

**Mention patterns:** Set via `messages.groupChat.mentionPatterns`

### Workspace
The **workspace** is a directory that stores:
- Project context files (`MEMORY.md`, `USER.md`, `SOUL.md`)
- Daily memory files (`memory/YYYY-MM-DD.md`)
- Agent-specific data and caches
- Tool artifacts and temporary files

**Default location:** `~/.openclaw/workspace`
**Config override:** `agents.defaults.workspace`

### Channel
A **channel** is a chat platform or plugin that sends/receives messages.

**Built-in channels:**
- WhatsApp, Telegram, Discord, Slack, Signal
- iMessage (macOS only), Google Chat
- IRC, Microsoft Teams, Matrix, Feishu, LINE, etc.
- WebChat (browser-based control UI)

**Channel config:** `channels.<provider>` (e.g., `channels.whatsapp`)

## Agent runtime concepts

### Thinking / Reasoning
Controls the agent's internal reasoning mode.

**Values:**
- `"off"` (hidden) — Reasoning not exposed (default)
- `"on"` — Show reasoning traces in responses
- `"stream"` — Stream reasoning token-by-token

**Override:** `/reasoning` command or per-session `model` override

### Model alias
Short names for model IDs to make them easier to reference.

**Example:**
```json5
{
  agents: {
    defaults: {
      models: {
        "anthropic/claude-sonnet-4-5": { alias: "Sonnet" },
        "openai/gpt-5.2": { alias: "GPT" },
      },
    },
  },
}
```

**Usage:** `/model Sonnet` switches to that model.

### Model fallback
Secondary models to use if the primary fails.

**Example:**
```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-sonnet-4-5",
        fallbacks: ["openai/gpt-5.2"],
      },
    },
  },
}
```

**Use case:** Auth rotation, quota exhaustion, or outages.

## Tool concepts

### Tool
A **tool** is a callable function available to agents. Tools include:
- `exec` — Run shell commands
- `browser` — Control web browsers
- `web_search` — Search the web (Brave API)
- `message` — Send messages to channels
- `sessions_spawn` — Spawn sub-agents
- `cron` — Schedule tasks and reminders
- `image` — Analyze images with vision models

**Permissions:** Configured per agent or session.

### Elevated command
A command that requires user approval before execution.

**Examples:**
- System-level commands (sudo, apt-get, etc.)
- Destructive operations (rm -rf, format, etc.)
- Commands that send emails or post publicly

**Approval flow:** User runs `/approve allow-once` or `/approve allow-always`

### Sandbox
An isolated execution environment for tools.

**Types:**
- `inherit` — Use parent's environment (default for most tools)
- `require` — Force isolated environment (e.g., containers)
- Filesystem sandboxing — Restrict paths to workspace

## Session concepts

### Main session
The primary conversation session between the user and the agent.

**Characteristics:**
- Full workspace access
- MEMORY.md available
- Full tool permissions
- Can spawn sub-agents

### Sub-agent
An isolated agent session spawned by the main agent.

**Use cases:**
- Coding tasks (via `coding-agent` skill)
- Parallel processing
- Isolated environments (different models, tools)

**Lifecycle:** Independent of parent; can be steered or killed.

### Heartbeat
A periodic check-in message from the user (or automation) to trigger proactive tasks.

**Default prompt:** `Read HEARTBEAT.md if it exists (workspace context). Follow it strictly. Do not infer or repeat old tasks from prior chats. If nothing needs attention, reply HEARTBEAT_OK.`

**Use cases:**
- Checking email/calendar
- Monitoring projects
- Background tasks
- Memory maintenance

## Node concepts

### Pairing
The process of connecting a mobile device to the Gateway.

**Methods:**
- QR code scan (mobile app)
- Manual pairing code
- Tailscale identity (automatic on tailnet)

### Canvas
A live UI rendered on a mobile device from the agent.

**Capabilities:**
- Presenting structured data
- Interactive elements (buttons, inputs)
- Screen sharing and snapshots
- Remote control via node

## Automation concepts

### Cron job
A scheduled task that runs at specific times or intervals.

**Schedule types:**
- `at` — One-shot at absolute time
- `every` — Recurring interval
- `cron` — Cron expression

**Payload types:**
- `systemEvent` — Inject text into main session
- `agentTurn` — Run agent with a message (isolated sessions only)

### Hook
An automation trigger tied to specific events.

**Hook types:**
- `beforeAgentTurn` — Run before agent processes a message
- `afterAgentTurn` — Run after agent responds
- `onSystemEvent` — Run on Gateway events

## Networking concepts

### Tailnet
A private network created by Tailscale for secure device-to-device communication.

**Use case:** Connect mobile nodes to Gateway without public Internet exposure.

### Reverse tunnel
A tunnel from Gateway to a public endpoint for remote access.

**Tools:**
- Cloudflare tunnel (`cloudflared`)
- ngrok
- SSH reverse tunnels

## Provenance
- **Source:** `docs/concepts/`, AGENTS.md, README.md
- **Last validated:** 2026-03-18 (against openclaw@latest from GitHub)
