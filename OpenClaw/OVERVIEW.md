# OpenClaw Overview

> Current as of 2026-08-19 (upstream `7a82d8b0f25`).

## What It Is

OpenClaw is a **self-hosted gateway** connecting chat apps (WhatsApp, Telegram, Discord, iMessage, Slack, Signal, and many more) to AI agents. Run a single Gateway process on your machine; message your AI assistant from anywhere.

## Architecture

```
Chat Apps (WhatsApp/Telegram/Discord/Slack/Signal/iMessage/...)
                    │
                    ▼
            ┌───────────────┐
            │   Gateway     │  ← Control plane (ws://127.0.0.1:18789)
            │  (Node.js)    │
            └───────┬───────┘
                    │
     ┌──────────────┼──────────────┐
     │              │              │
     ▼              ▼              ▼
  Pi Agent      CLI/WebUI      Mobile Nodes
  (RPC mode)   (openclaw ...)  (iOS/Android/macOS)
```

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Gateway** | Single control plane for sessions, routing, channels |
| **Session** | Conversation context (main=direct chat, isolated=per-agent, group=per-group) |
| **Channel** | Messaging surface (WhatsApp, Telegram, Discord, etc.) |
| **Node** | Paired device (iOS/Android/macOS) with camera/mic/canvas |
| **Skill** | Bundled capability package (browser, web search, coding agent, etc.) |
| **Plugin** | Extension package for additional channels/providers/tools |
| **Heartbeat** | Periodic agent turns in the main session for proactive work |
| **Sandbox** | Isolated tool execution environment (Docker/Podman/SSH/OpenShell) |
| **Streaming** | Live preview updates during generation (partial/block/progress modes) |

## Quick Start

```bash
# Install
npm install -g openclaw@latest

# Onboard (guided setup)
openclaw onboard --install-daemon

# Start gateway
openclaw gateway

# Send message
openclaw message send --to +1234567890 --message "Hello"

# Chat with agent
openclaw agent --message "What's on my calendar?"
```

## Requirements

- **Node.js**: v22+ (v24 recommended)
- **API Key**: From your LLM provider (OpenAI, Anthropic, etc.)
- **Runtime**: 5 minutes to get running

## Config Location

```
~/.openclaw/openclaw.json     # Main config (JSON5, strict schema validation)
~/.openclaw/credentials/       # Auth credentials
~/.openclaw/sessions/          # Session data (SQLite)
```

## Channels Supported

**Built-in**: WhatsApp, Telegram, Discord, Slack, Google Chat, Signal, iMessage (BlueBubbles), IRC, WebChat

**Via Plugins**: Microsoft Teams, Matrix, Feishu, LINE, Mattermost, Nextcloud Talk, Nostr, Synology Chat, Tlon, Twitch, Zalo, and more

## Streaming Preview (Current Defaults)

| Channel | Default Mode | Behavior |
|---------|-------------|----------|
| Telegram | `progress` | Editable status draft with tool progress; final answer as normal message |
| Slack | `progress` | Block Kit session card; final answer as separate message |
| Discord | `off` | No preview streaming by default |
| Mattermost | `partial` | Single draft preview finalized in place |
| MS Teams | `partial` | Native partial stream |

All channels support: `off`, `partial`, `block`, `progress` modes via `channels.<channel>.streaming.mode`.

## Security Model

- **DM pairing required by default** (`dmPolicy="pairing"`)
- Unknown senders get a time-limited pairing code (1h expiry)
- Approve: `openclaw pairing approve <channel> <code>`
- **Sandboxing off by default** (`agents.defaults.sandbox.mode="off"`)
- When enabled: Docker/Podman/SSH/OpenShell backends with `network: "none"`, `capDrop: ["ALL"]`
- Run `openclaw doctor` to audit security posture

## Heartbeat

- Default cadence: `30m` (1h for Anthropic OAuth/token auth)
- Default target: `owner` (operator DM from `commands.ownerAllowFrom`)
- `directPolicy`: `"allow"` (default) or `"block"` to suppress DM delivery
- Scheduled heartbeats require `cron.enabled: true`
- `isolatedSession: true` + `lightContext: true` for maximum token savings

## Key Commands

| Command | Purpose |
|---------|---------|
| `openclaw onboard` | Guided setup wizard |
| `openclaw gateway` | Start/control gateway |
| `openclaw agent` | Chat with AI agent |
| `openclaw message` | Send/receive messages |
| `openclaw channels` | Manage channel connections |
| `openclaw doctor` | Diagnose issues |
| `openclaw doctor --fix` | Auto-repair config keys |
| `openclaw config` | View/edit configuration |
| `openclaw cron` | Manage scheduled jobs |
| `openclaw pairing` | Approve/review pairing requests |
| `openclaw sessions` | List/manage sessions |

## Docs Structure

```
docs/
├── start/        # Getting started, onboarding
├── channels/     # Per-channel setup guides
├── concepts/     # Sessions, routing, models, streaming, memory
├── tools/        # Browser, skills, subagents
├── gateway/      # Configuration, security, sandboxing, heartbeat
├── nodes/        # iOS/Android/macOS companion apps
├── install/      # Installation methods (npm, Docker, Nix)
├── automation/   # Cron, webhooks, heartbeat vs cron
├── plugins/      # Plugin system, marketplace
└── help/         # FAQ, troubleshooting
```

## Source

- Repo: <https://github.com/openclaw/openclaw>
- Docs: <https://docs.openclaw.ai>
- Community: <https://discord.gg/clawd>
