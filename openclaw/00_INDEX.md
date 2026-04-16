---
summary: "OpenClaw docs pack - current release behavior (not changelog)"
read_when:
  - Working on OpenClaw configuration or integration
  - Need current command reference or tool guidance
title: "OpenClaw Documentation Pack"
sidebarTitle: "Current Docs"
---

# OpenClaw Docs Pack (Current-State, LLM-Ready)

This pack summarizes **current OpenClaw behavior** (not changelog narration).

## Current Status
- **Upstream source repo:** `openclaw-src`
- **Upstream commit processed:** e485f24301d8aee20cb0842494f27dbb19c5871c
- **Processed on:** 2026-04-16
- **Status:** ✅ Fully regenerated from current source

## Documentation Structure

- **[01_PROJECT_OVERVIEW.md](./01_PROJECT_OVERVIEW.md)** - Project overview and architecture
- **[02_ARCHITECTURE_AND_RUNTIME.md](./02_ARCHITECTURE_AND_RUNTIME.md)** - Gateway architecture and runtime components
- **[03_CLI_SURFACE_MAP.md](./03_CLI_SURFACE_MAP.md)** - Complete CLI command reference
- **[04_CHANNELS_AND_ROUTING.md](./04_CHANNELS_AND_ROUTING.md)** - Channel configurations and routing
- **[05_TOOLS_AND_AUTOMATION.md](./05_TOOLS_AND_AUTOMATION.md)** - Tool profiles and automation
- **[06_SECURITY_AND_OPERATIONS.md](./06_SECURITY_AND_OPERATIONS.md)** - Security model and operations
- **[07_AGENT_WORKSPACE_MEMORY_SKILLS.md](./07_AGENT_WORKSPACE_MEMORY_SKILLS.md)** - Agent workspace, memory, and skills
- **[08_QUICK_TASK_ROUTER.md](./08_QUICK_TASK_ROUTER.md)** - Quick task routing guide

## Key Current Features

### 🦞 Core Architecture
- WebSocket gateway serving all messaging surfaces
- Control-plane clients (CLI, macOS app, web UI, automations) via WebSocket
- Node connections (macOS/iOS/Android/headless) with explicit roles/caps
- One Gateway per host (owns WhatsApp session)

### 📱 Supported Channels
- WhatsApp (via Baileys), Telegram (via grammY), Slack, Discord, Signal, iMessage
- Matrix (E2EE with SSSS bootstrap), Google Chat, MS Teams, IRC, Nostr, LINE, QQ, Zalo, Feishu, BlueBubbles, Nextcloud Talk, Mattermost, Synology Chat, Tlon
- WebChat (embedded in Control UI)

### 🛠️ Tool Profiles
- **minimal**: Basic file operations
- **coding**: Development tools (read, write, edit, exec, code_execution, web_search)
- **full**: All tools including browser, media, TTS, nodes, canvas, sessions

### 🔒 Security
- SecretRef credential resolution (env vars, file-based, inline)
- SSRF guard on browser, MCP, and media paths
- Exec approvals with per-channel DM delivery (Discord, Slack)
- SendPolicy deny suppresses delivery (not inbound processing)
- Config mutation guards on dangerous writes

### 🧠 Memory & Active Memory
- Memory search with embedding providers (OpenAI, Gemini, Ollama, GitHub Copilot)
- Active Memory plugin: hidden prompt prefix injection before main reply
- Dreaming: automated memory consolidation (storage.mode defaults to "separate")
- QMD manager with session files support
