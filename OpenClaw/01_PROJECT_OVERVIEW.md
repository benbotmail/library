---
summary: "OpenClaw project overview - what it is, how it runs, what it connects to"
read_when:
  - You need a mental model of the whole system
  - Onboarding onto OpenClaw for the first time
  - Explaining OpenClaw to someone else
title: "Project Overview"
---

# OpenClaw — Project Overview

## What It Is

OpenClaw is a self-hosted AI assistant gateway. One process (the Gateway) connects your chosen LLM providers to your messaging platforms, tools, and local machine.

Core idea: **one agent, many surfaces**. The same assistant that answers in Telegram can also work in Discord, Slack, WhatsApp, WeChat, a web UI, or directly via CLI.

## Architecture

```
┌──────────────┐     ┌─────────────────────────┐     ┌──────────────┐
│  Messaging   │────▶│      Gateway            │────▶│  LLM         │
│  Channels    │◀────│  (Node.js daemon)       │◀────│  Providers   │
└──────────────┘     │                         │     └──────────────┘
                     │  - Session management   │
┌──────────────┐     │  - Tool dispatch        │     ┌──────────────┐
│  Control UI  │────▶│  - Memory / Skills      │────▶│  Nodes       │
│  (web/macOS) │◀────│  - Cron / Heartbeat     │     │  (devices)   │
└──────────────┘     │  - Plugin system        │     └──────────────┘
                     └─────────────────────────┘
```

## Key Components

### Gateway
The central daemon (`openclaw gateway start`). Owns:
- WebSocket server for control-plane clients
- Channel plugins for each messaging platform
- Agent runtime (embedded PI agent with tool loop)
- Session persistence and transcript management
- Config management (`~/.openclaw/openclaw.json`, JSON5 format)

### Channels
Messaging platforms that connect through plugins. Each channel auto-starts when its config block exists. Supported: WhatsApp, Telegram, Slack, Discord, Signal, iMessage (BlueBubbles), Matrix, Google Chat, MS Teams, IRC, Nostr, LINE, QQ, Zalo, Feishu, Nextcloud Talk, Mattermost, Synology Chat, Tlon, Twitch, **WeChat** (external plugin), WebChat.

### Providers
LLM backends: OpenAI, Anthropic, Google/Gemini (dedicated plugin transport), Azure, Ollama, OpenRouter, Together, Fireworks, Groq, DeepSeek, Mistral, xAI, Z.AI, and 30+ more. Each configured under `models.providers`.

### Tools
Functions the agent can call: `read`, `write`, `edit`, `exec`, `web_search`, `web_fetch`, `browser`, `message`, `cron`, `gateway`, `sessions_spawn`, `image`, `tts`, `memory_search`, `memory_get`, `nodes`, `canvas`, etc.

### Nodes
Paired devices (macOS, iOS, Android, headless) that extend the agent's reach: camera, screen recording, location, push notifications. Node browser proxy enables zero-config remote browser access.

### Plugins
Extension system for channels, providers, and capabilities. Bundled plugins (browser, channels, memory-core, active-memory, etc.) ship with OpenClaw; community/external plugins install via `openclaw plugins install`.

### Memory
- **Memory search**: Semantic search over `MEMORY.md` and `memory/*.md` using embeddings from individual provider extensions (OpenAI, Gemini, Ollama, GitHub Copilot, Voyage, Bedrock, LMStudio, Mistral)
- **Active Memory**: Plugin-owned blocking sub-agent that injects relevant memories before each reply (hidden prompt prefix, not user-visible)
- **Dreaming**: Automated memory consolidation via cron (`memory-core` plugin)

## Configuration

Single file: `~/.openclaw/openclaw.json` (JSON5 — comments and trailing commas allowed). All fields optional; safe defaults when omitted.

Key sections:
- `models.providers` — LLM backends and API keys
- `channels.<platform>` — channel-specific settings (DM/group policy, streaming, media limits)
- `channels.defaults` — shared group policy, heartbeat defaults, contextVisibility
- `channels.modelByChannel` — per-channel and per-topic model overrides
- `agents.defaults` — agent behavior, model, heartbeat, thinking
- `plugins.entries` — plugin configuration
- `messages.tts` — text-to-speech
- `tools` — tool policy and experimental flags
- `browser` — browser profiles, SSRF policy, CDP config

## CLI

```bash
openclaw gateway start|stop|restart|status  # Daemon lifecycle
openclaw configure                          # Interactive setup wizard
openclaw onboard                            # Full onboarding flow
openclaw doctor                             # Diagnose and repair issues
openclaw memory status --deep               # Memory health check
openclaw config schema                      # Print live JSON Schema
openclaw browser status                     # Browser status
openclaw plugins list                       # Installed plugins
```

## Current Version Notes

- Config format: JSON5, all fields optional, strict schema validation
- Browser: bundled plugin, multi-profile, direct WebSocket CDP, SSRF guard
- Google Gemini: transport moved into dedicated plugin
- WeChat: external plugin via `@tencent-weixin/openclaw-weixin`
- Embedding providers: individual extensions (voyage, google, ollama, mistral, lmstudio, bedrock)
- Telegram: topic-level model overrides in `modelByChannel`
- Heartbeat defaults configurable in `channels.defaults.heartbeat`
- OAuth hardened for Codex CLI bridge and concurrent agent auth
- Matrix: E2EE support, subagent hooks, thread binding, reaction auth