---
summary: "Tool profiles, browser control, TTS, media, and automation tools"
read_when:
  - Looking up a tool's parameters or behavior
  - Configuring browser, TTS, or media tools
  - Understanding tool policy and experimental tools
title: "Tools and Automation"
---

# Tools and Automation

## Tool Profiles

Tools available to the agent depend on the configured profile:

### minimal
Basic file operations: `read`, `write`, `edit`

### coding
Development tools: `read`, `write`, `edit`, `exec`, `code_execution`, `web_search`

### full
All tools: everything in coding plus `browser`, `message`, `cron`, `gateway`, `nodes`, `canvas`, `sessions_spawn`, `image`, `tts`, `memory_search`, `memory_get`, `web_fetch`

## Key Tools

### exec
Execute shell commands. Supports:
- Background execution with `yieldMs`
- PTY mode for TTY-required CLIs (Codex, Claude Code, etc.)
- Elevated permissions (host-level, when allowed)
- Approval flow for sensitive commands (secrets redacted)
- Sandbox execution with `host: "auto"` (default)
- `host: "node"` for remote execution on paired nodes

Config:
- `tools.exec.notifyOnExit` (default: true): heartbeat on background exit
- `tools.exec.security`: `deny` | `allowlist` | `full`
- `tools.exec.ask`: `off` | `on-miss` | `always`
- `tools.exec.strictInlineEval`: force approval for `python -c`, `node -e`, etc.
- `tools.exec.safeBins`: stdin-only safe binaries without allowlist
- `tools.exec.pathPrepend`: directories prepended to PATH

### browser
Control web browser via CDP. **Now a bundled plugin** — disable or replace without touching core.

Profiles:
- `openclaw`: isolated managed browser (default)
- `user`: logged-in user browser via Chrome MCP
- `chrome-relay`: Chrome extension / Browser Relay toolbar button
- Custom profiles: `work`, `remote`, `browserless`, `browserbase`, etc.

Key features:
- Snapshot (DOM tree / accessibility tree)
- Screenshot, PDF
- Navigate, click, type, fill, select, drag
- Tab management (list/open/focus/close)
- **SSRF guard**: enforced on all browser navigation. `browser.ssrfPolicy.dangerouslyAllowPrivateNetwork` opt-in for private network access.
- **Direct WebSocket CDP**: three URL shapes — HTTP discovery, direct `/devtools/` WebSocket, bare WebSocket root
- **Node browser proxy**: zero-config remote gateway setups via node host
- **Hosted providers**: Browserless, Browserbase with direct WSS endpoints
- Remote CDP timeout config: `remoteCdpTimeoutMs`, `remoteCdpHandshakeTimeoutMs`

Plugin control:
```json5
{
  plugins: { entries: { browser: { enabled: false } } },  // disable bundled browser
  browser: { enabled: true, defaultProfile: "openclaw" },
}
```

If `plugins.allow` is set, must include `browser` for browser tool to load.

### web_search / web_fetch
- `web_search`: Brave Search API (1-10 results, region/language filters, freshness)
- `web_fetch`: HTTP fetch + HTML→markdown extraction

### message
Send messages via channel plugins. Supports: send, edit, delete, react, poll, topic-create. Channel-specific features vary. Media uploads via `filePath`, `buffer`, or `media` URL.

### cron
Manage scheduled jobs. Schedule types: `at` (one-shot), `every` (interval), `cron` (expression). Payload types: `systemEvent` (main session), `agentTurn` (isolated session). Delivery: `none`, `announce`, `webhook`.

### gateway
Restart, apply config, or run updates. Protects `tools.exec.ask` / `tools.exec.security` from config writes.

### sessions_spawn
Spawn isolated sub-agents or ACP coding sessions. Modes: `run` (one-shot) or `session` (persistent/thread-bound). Sub-agents inherit parent workspace.

### image
Analyze images with the configured image model. Single or batch (up to 20).

### tts
Convert text to speech. Supported providers: **ElevenLabs**, **Google Gemini**, **Microsoft** (Edge TTS, no API key needed), **MiniMax**, **OpenAI**.

```json5
{
  messages: {
    tts: {
      auto: "always",  // off | always | summary_only
      provider: "openai",  // auto-detected when unset
      voice: "alloy",
    },
  },
}
```

### memory_search / memory_get
Semantic search over `MEMORY.md` and `memory/*.md`. Embedding providers are **individual extensions** (not memory-host-sdk):
- **OpenAI**: `text-embedding-3-small` (recommended)
- **Gemini**: `gemini-embedding-001`
- **Ollama**: `nomic-embed-text` (local)
- **GitHub Copilot**: embedding access
- **Voyage**: voyage-3 embeddings
- **Bedrock**: AWS Bedrock embeddings
- **LMStudio**: local model studio
- **Mistral**: Mistral embeddings

### nodes
Discover and control paired devices: camera, screen recording, location, notifications, remote command execution. Node browser proxy for zero-config remote browser access.

### canvas
Present/eval/snapshot UI canvases on nodes. A2UI push for agent-generated UI.

## Tool Policy

Configurable under `tools`:
- `tools.exec.ask`: approval mode for exec commands
- `tools.exec.security`: `deny` | `allowlist` | `full`
- `tools.experimental.planTool`: enables structured `update_plan` tool

## Experimental Tools

```json5
{
  tools: {
    experimental: {
      planTool: true,  // enable structured update_plan for multi-step work
    },
  },
}
```

## Tool Loop Detection

Prevents infinite tool call loops by tracking repeated patterns. Unknown-tool retries counted only when streamed.

## Tool Result Handling

- Large results truncated to fit context window
- Tool result context guard prevents accumulated outputs from overflowing
- Truncation configurable with per-source limits