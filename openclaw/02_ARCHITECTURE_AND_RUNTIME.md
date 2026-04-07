---
summary: "Gateway architecture, components, and client flows"
read_when:
  - Working on gateway protocol, clients, or transports
  - Understanding OpenClaw's technical architecture
title: "Architecture and Runtime"
sidebarTitle: "Architecture"
---

# Gateway Architecture

## Overview

- A single long-lived **Gateway** owns all messaging surfaces (WhatsApp via Baileys, Telegram via grammY, Slack, Discord, Signal, iMessage, WebChat).
- Control-plane clients (macOS app, CLI, web UI, automations) connect to the Gateway over **WebSocket** on the configured bind host (default `127.0.0.1:18789`).
- **Nodes** (macOS/iOS/Android/headless) also connect over **WebSocket**, but declare `role: node` with explicit caps/commands.
- One Gateway per host; it is the only place that opens a WhatsApp session.
- The **canvas host** is served by the Gateway HTTP server under `/canvas` endpoint.

## Component Architecture

### 🌐 Gateway Core
- **WebSocket server** for client connections
- **HTTP server** for canvas and static files
- **Message dispatcher** for routing and dispatch
- **Session manager** for session persistence
- **Configuration manager** for runtime config

### 🔗 Client Connections
```typescript
// Control-plane clients (CLI, app, web UI)
ControlPlaneClient {
  role: "control"
  capabilities: ["config", "sessions", "tasks", "status"]
  transport: WebSocket
}

// Node clients (mobile, headless)
NodeClient {
  role: "node" 
  capabilities: ["camera", "screen", "location", "notifications", "exec"]
  transport: WebSocket
}
```

### 📡 Transport Layer
- **WebSocket** for real-time client communication
- **Bonjour/Zeroconf** for local discovery
- **Wide-area DNS** for remote gateway discovery (optional)
- **SSH tunnel** support for remote gateway access

## Message Flow

### 1. Client → Gateway
Clients send messages to the Gateway for processing and routing to channels.

### 2. Gateway → Channel
Gateway routes messages to appropriate messaging platforms with target and content.

### 3. Channel → Gateway
Channels respond with delivery status and message metadata.

## Configuration System

### Runtime Configuration
- **Gateway settings** - bind host, ports, authentication
- **Channel configurations** - Telegram, WhatsApp, Slack, Discord settings
- **Model providers** - OpenAI, Anthropic, MiniMax, GLM, Ollama configurations
- **Tool profiles** - minimal, coding, messaging, full configurations
- **Security policies** - allowlists, denylists, sandbox settings
- **Plugin system** - custom tool and integration support

### Configuration Loading
1. **File-based** primary configuration (`~/.openclaw/config.json5`)
2. **Environment variables** override (`OPENCLAW_*`)
3. **Secret references** for sensitive values
4. **Runtime validation** with Zod schema

## Session Management

### Session Types
- **Main session** - Direct human interaction
- **Sub-agent sessions** - Background task execution
- **ACP sessions** - ACP harness coding sessions
- **Node sessions** - Remote device interaction

### Session Persistence
- **File-based** storage with JSON format
- **Automatic cleanup** with configurable retention
- **Usage tracking** for model costs and token counts
- **Memory system** with daily and long-term storage

## Tool System Architecture

### Tool Profiles
```typescript
type ToolProfileId = "minimal" | "coding" | "messaging" | "full"

interface ToolProfile {
  allow: string[]  // Tool whitelist
  deny: string[]   // Tool blacklist  
}
```

### Tool Categories
- **Files** - read, write, edit, apply_patch
- **Runtime** - exec, process, code_execution
- **Web** - web_search, browser, fetch
- **Memory** - memory_search, memory_get
- **Sessions** - sessions_list, sessions_send
- **UI** - canvas, browser, nodes
- **Messaging** - message, cron, tts
- **Automation** - exec, cron, process
- **Nodes** - nodes_*, camera, screen
- **Agents** - sessions_spawn, subagents
- **Media** - image, canvas

## Security Model

### Gateway Authentication
- **Token-based** authentication for remote connections
- **Password protection** for sensitive operations
- **Role-based** access control (control vs node)

### Tool Security
- **Allowlist/denylist** per tool profile
- **Sandboxed execution** for shell commands
- **Secret isolation** for sensitive data
- **Runtime permission** validation

### Channel Security
- **Allowlist matching** for channel access
- **Secret reference** system for tokens
- **Runtime validation** of channel configurations

## Runtime Environment

### Node Types
- **macOS** - Full feature support (camera, screen, notifications)
- **iOS** - Limited support (camera, location, notifications)
- **Android** - Limited support (camera, screen, location)
- **Linux** - Headless support (exec, file operations)

### Resource Management
- **Memory** usage tracking per session
- **CPU limits** for background processes
- **Network** timeout configuration
- **Disk** cleanup for temporary files

## Health Monitoring

### Gateway Health
- **WebSocket connectivity** status
- **Channel reachability** probes
- **Resource usage** tracking
- **Session metrics** collection

### Client Health
- **Connection status** monitoring
- **Task completion** tracking
- **Error reporting** and logging
- **Performance metrics** collection