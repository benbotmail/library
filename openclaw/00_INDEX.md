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
- **Upstream commit processed:** `3b9dab0ece4643a9643e6a45459f5c709d3ce320` 
- **Processed on:** 2026-03-30
- **Status:** ✅ Fully regenerated from current source

## Documentation Structure

- **[01_PROJECT_OVERVIEW.md](./01_PROJECT_OVERVIEW.md)** - Project overview and architecture
- **[02_ARCHITECTURE_AND_RUNTIME.md](./02_ARCHITECTURE_AND_RUNTIME.md)** - Gateway architecture and runtime components
- **[03_CLI_SURFACE_MAP.md](./03_CLI_SURFACE_MAP.md)** - Complete CLI command reference
- **[04_CHANNELS_AND_ROUTING.md](./04_CHANNELS_AND_ROUTING.md)** - Channel configurations and routing
- **[05_TOOLS_AND_AUTOMATION.md](./05_TOOLS_AND_AUTOMATION.md)** - Tool profiles and automation
- **[06_SECURITY_AND_OPERATIONS.md](./06_SECURITY_AND_OPERATIONS.md)** - Security model and operations
- **[07_AGENT_WORKSPACE_MEMORY_SKILLS.md](./07_AGENT_WORKSPACE_MEMORY_SKILLS.md)** - Agent workspace system
- **[08_QUICK_TASK_ROUTER.md](./08_QUICK_TASK_ROUTER.md)** - Quick task routing guide

## Key Current Features

### 🦞 Core Architecture
- WebSocket gateway serving all messaging surfaces
- Control-plane clients (CLI, macOS app, web UI, automations) via WebSocket
- Node connections (macOS/iOS/Android/headless) with explicit roles/caps
- One Gateway per host (owns WhatsApp session)

### 📱 Supported Channels
- WhatsApp (via Baileys)
- Telegram (via grammY)
- Slack
- Discord
- Signal
- iMessage
- WebChat

### 🛠️ Tool Profiles
- **minimal**: Basic file operations
- **coding**: Development tools (read, write, edit, exec, code_execution, web_search)
- **messaging**: Communication tools (message, browser, canvas, nodes)
- **full**: All available tools

### 🎯 CLI Commands
- **`openclaw [command]`** - Main CLI interface
- **`openclaw gateway [run|status|health|discover]`** - Gateway management
- **`openclaw config [get|set|unset|schema]`** - Configuration management
- **`openclaw status`** - Channel health and session summary
- **`openclaw sessions [list|cleanup]`** - Session management
- **`openclaw tasks [list|audit|cancel]`** - Background task management

This documentation reflects the **exact current state** of OpenClaw source code as of commit `3b9dab0ece4643a9643e6a45459f5c709d3ce320`.