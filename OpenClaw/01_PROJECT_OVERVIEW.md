# 01 — Project Overview

## What OpenClaw is
OpenClaw is a self-hosted personal AI assistant gateway. It bridges messaging channels (WhatsApp, Telegram, Discord, Slack, Signal, iMessage, WebChat, etc.) to agent runtimes and tools.

## Core value proposition
- One gateway process as control plane
- Multi-channel inbox + routing
- Agent/tool-native workflows (sessions, tools, memory, automation)
- Local-first deployment and ownership

## Runtime expectations
- Node.js >= 22
- Primary onboarding path: `openclaw onboard --install-daemon`
- Typical operator flow: gateway service + channels + optional nodes/apps

## Primary documentation hubs (in repo)
- `openclaw-src/README.md` (high-level platform + links)
- `openclaw-src/docs/index.md` (docs landing / navigation)
- `openclaw-src/docs/start/*` (onboarding and quickstart)

## Mental model
Think of OpenClaw as:
- **Gateway = control plane**
- **Channels = ingress/egress**
- **Agents = reasoning workers**
- **Tools = action surface**
- **Sessions = stateful conversation units**
- **Nodes = device-extended capabilities**
