# OpenClaw Overview

> Personal AI assistant gateway — self-hosted, multi-channel, agent-native

## What It Is

OpenClaw is a **self-hosted gateway** connecting chat apps (WhatsApp, Telegram, Discord, iMessage, Slack, etc.) to AI agents. Run a single Gateway process on your machine; message your AI assistant from anywhere.

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
  (RPC mode)   (openclaw ...)  (iOS/Android)
```

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Gateway** | Single control plane for sessions, routing, channels |
| **Session** | Conversation context (main=direct chat, isolated=per-agent) |
| **Channel** | Messaging surface (WhatsApp, Telegram, Discord, etc.) |
| **Node** | Paired device (iOS/Android/macOS) with camera/mic/canvas |
| **Skill** | Bundled capability (browser, web search, etc.) |
| **Plugin** | Extension package for additional channels/features |

## Quick Start

```bash
# Install
npm install -g openclaw@latest

# Onboard (guided setup)
openclaw onboard --install-daemon

# Start gateway
openclaw gateway --port 18789

# Send message
openclaw message send --to +1234567890 --message "Hello"

# Chat with agent
openclaw agent --message "What's on my calendar?"
```

## Requirements

- **Node.js**: v22+ (v24 recommended)
- **API Key**: From your LLM provider (OpenAI, Anthropic, etc.)
- **Runtime**: 5 minutes to get running

## Channels Supported

**Built-in**: WhatsApp, Telegram, Discord, Slack, Google Chat, Signal, iMessage (BlueBubbles), IRC, Matrix, Feishu, LINE, Mattermost, Nextcloud Talk, Nostr, Synology Chat, Twitch, Zalo, WebChat

**Via Plugins**: Microsoft Teams, additional protocols

## Config Location

```
~/.openclaw/openclaw.json    # Main config
~/.openclaw/credentials/      # Auth credentials
~/.openclaw/sessions/         # Session data
```

## Security Model

- **Default**: DM pairing required (`dmPolicy="pairing"`)
- Unknown senders get a pairing code
- Approve with: `openclaw pairing approve <channel> <code>`
- Run `openclaw doctor` to audit security posture

## Key Commands

| Command | Purpose |
|---------|---------|
| `openclaw onboard` | Guided setup wizard |
| `openclaw gateway` | Start/control gateway |
| `openclaw agent` | Chat with AI agent |
| `openclaw message` | Send/receive messages |
| `openclaw channels` | Manage channel connections |
| `openclaw doctor` | Diagnose issues |
| `openclaw config` | View/edit configuration |

## Docs Structure

```
docs/
├── start/        # Getting started, onboarding
├── channels/     # Per-channel setup guides
├── concepts/     # Sessions, routing, models
├── tools/        # Browser, skills, subagents
├── gateway/      # Configuration, security, remote access
├── nodes/        # iOS/Android/macOS companion apps
├── install/      # Installation methods (npm, Docker, Nix)
├── automation/   # Cron, webhooks
└── help/         # FAQ, troubleshooting
```

## Source

- Repo: https://github.com/openclaw/openclaw
- Docs: https://docs.openclaw.ai
- Community: https://discord.gg/clawd
