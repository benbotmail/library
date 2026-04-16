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
- PTY mode for TTY-required CLIs
- Elevated permissions (host-level, when allowed)
- Approval flow for sensitive commands
- Approval prompts redact secrets

### browser
Control web browser via CDP (Chrome DevTools Protocol).

Profiles:
- `openclaw`: isolated managed browser (default)
- `user`: logged-in user browser on local host
- `chrome-relay`: Chrome extension / Browser Relay toolbar button

Key features:
- Snapshot (DOM tree / accessibility tree)
- Screenshot
- Navigate, click, type, fill, select, drag
- Tab management
- **SSRF guard**: enforced on snapshot, screenshot, and tab routes. Default hostname SSRF relaxed for loopback. `cdp-reachability-policy` manages CDP readiness under strict defaults.

### web_search / web_fetch
- `web_search`: Brave Search API (1-10 results, region/language filters)
- `web_fetch`: HTTP fetch + HTML→markdown extraction

### message
Send messages via channel plugins. Supports: send, edit, delete, react, poll, topic-create. Channel-specific features vary.

### cron
Manage scheduled jobs. Schedule types: `at`, `every`, `cron`. Payload types: `systemEvent` (main session), `agentTurn` (isolated session). Delivery: `none`, `announce`, `webhook`.

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

TTS directives in replies control when/how audio is sent. Bundled TTS providers register correctly with the gateway.

### memory_search / memory_get
Semantic search over `MEMORY.md` and `memory/*.md`. Requires embedding provider configuration (OpenAI, Gemini, Ollama, or GitHub Copilot embeddings).

### nodes
Discover and control paired devices: camera, screen recording, location, notifications, remote command execution.

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

Experimental features are opt-in preview surfaces. Shape and behavior may change faster than stable config.

## Tool Loop Detection

Prevents infinite tool call loops by tracking repeated patterns. Unknown-tool retries are only counted when streamed.

## Tool Result Handling

- Large results are truncated to fit context window
- Tool result context guard prevents accumulated outputs from overflowing
- Truncation is configurable with per-source limits
- Failed tool calls may be retried based on error classification
