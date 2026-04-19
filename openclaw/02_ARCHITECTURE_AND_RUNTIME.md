---
summary: "Gateway architecture, runtime, sessions, and plugin system internals"
read_when:
  - Understanding how sessions work
  - Debugging gateway startup or channel issues
  - Working with the plugin system
title: "Architecture and Runtime"
---

# Architecture and Runtime

## Gateway Process

The gateway is a long-running Node.js daemon. It:
1. Reads `~/.openclaw/openclaw.json` (JSON5)
2. Starts configured channel plugins
3. Opens WebSocket server for control-plane clients (CLI, macOS app, web UI)
4. Runs the embedded PI agent for each session
5. Manages cron jobs, heartbeat, and background tasks

## Session Model

Each conversation gets a persistent session identified by a session key (e.g., `agent:main:telegram:group:-1001234567890`, `agent:main:discord:channel:123456:thread:987654`).

Session key shapes:
- **Main DM**: `agent:<agentId>:<mainKey>` (default: `agent:main:main`)
- **Groups**: `agent:<agentId>:<channel>:group:<id>`
- **Threads**: append `:thread:<threadId>` (Slack/Discord)
- **Topics**: embed `:topic:<topicId>` (Telegram forums)

Sessions store:
- Transcript (conversation history in JSONL)
- Model override (set via `/model`)
- Thinking level
- Thread bindings (platform-specific routing)

Session reset clears transcript but preserves config-level settings. Reset policy is configurable.

### Session Types
- **Main session**: Direct chat with the human (loads `MEMORY.md`)
- **Isolated session**: Fresh session per run (cron, sub-agents)
- **Persistent ACP session**: Thread-bound coding agent session

### Session Scoping
`session.dmScope` controls DM isolation:
- `main`: all DMs share one session
- `per-peer`: one session per sender
- `per-channel-peer`: one session per sender per channel
- `per-account-channel-peer`: most granular (account + channel + peer)

## Agent Runtime

The embedded agent runs inside the gateway process:
- Receives inbound messages from channels
- Assembles system prompt with tool definitions, workspace files, skills
- Calls the configured LLM provider
- Dispatches tool calls (read, write, exec, browser, etc.)
- Streams replies back to the originating channel

### Context Engine
Manages prompt assembly and context window:
- Bootstrap files injected from workspace (`SOUL.md`, `USER.md`, `AGENTS.md`, etc.)
- Memory search results injected on demand
- Active Memory plugin injects hidden prefix before main reply
- Compaction when context window fills (reserves configurable percentage)
- Context-window guard tightens limits and bounds memory excerpts

### Compaction
When context fills up, the agent compacts older turns into a summary. Compaction reserve floor is capped to the model's context window (important for small models).

## Channel Plugin System

Channels are plugins that implement the channel interface:
- **Bundled**: Ship with OpenClaw (Telegram, Discord, Slack, WhatsApp, Matrix, etc.)
- **External**: Installed via `openclaw plugins install` (e.g., WeChat via `@tencent-weixin/openclaw-weixin`)

Each plugin provides:
- Inbound message handling (with deduplication)
- Outbound delivery (text, media, reactions, etc.)
- Command registration (slash commands, native commands)
- Account management (multi-account support)

### Plugin Loading
- Source alias resolution for development
- JITI loader with caching for fast reloads
- Manifest registry with validation
- Runtime boundary between plugin code and core
- `plugins.allow` can restrict which plugins load (must include `browser` if browser tool needed)

### Plugin Contracts
- Channel plugins: inbound/outbound message contract, pairing, media
- Provider plugins: model API contract, streaming, thinking config
- Tool plugins: register tool names, parameter schemas, handlers
- Extension providers: embedding providers as individual extensions (voyage, google, ollama, mistral, lmstudio, bedrock)

## Cron System

Two types:
1. **systemEvent**: Injects text into the main session
2. **agentTurn**: Runs an isolated agent turn (separate session, configurable model)

Cron jobs support:
- Schedule: `at` (one-shot), `every` (interval), `cron` (expression)
- Delivery: `none`, `announce` (to channel), `webhook`
- Wake events: `now` or `next-heartbeat`

## Heartbeat

Periodic agent turn (default every 30m for API-key auth, 1h for OAuth). Config:
- `agents.defaults.heartbeat.every`: interval
- `agents.defaults.heartbeat.directPolicy`: `"allow"` (default) or `"block"` — controls whether heartbeat can send DMs
- `agents.defaults.heartbeat.isolatedSession`: `true` for fresh session each time (~2-5K tokens vs ~100K)
- `agents.defaults.heartbeat.lightContext`: `true` for minimal bootstrap
- `agents.defaults.heartbeat.target`: delivery target channel
- `channels.defaults.heartbeat`: shared heartbeat display config (`showOk`, `showAlerts`, `useIndicator`)

## Tool Loop

The agent executes tools in a loop until the model produces a final text reply. Tool loop detection prevents infinite loops by tracking repeated tool call patterns.

### Tool Result Handling
- Large tool results are truncated to fit context window
- Tool result context guard prevents context overflow from accumulated tool outputs
- Failed tool calls may be retried based on error classification

## Startup Flow

1. Load config (`config.io.bestEffort` for resilience)
2. Initialize plugin registry
3. Start channel plugins (each auto-connects)
4. Start WebSocket server
5. Register gateway methods (RPC endpoints)
6. Schedule heartbeat and cron jobs
7. Run startup post-attach hooks