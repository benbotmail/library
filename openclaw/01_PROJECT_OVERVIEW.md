# 01 — Project Overview

OpenClaw is a self-hosted assistant gateway: channel traffic comes in, agents/tools run turns, and replies/actions are sent back through the originating surface.

Primary refs:
- `openclaw-src/README.md`
- `openclaw-src/docs/index.md`
- `openclaw-src/docs/start/getting-started.md`

## Core model
- **Gateway**: control plane for runtime, channels, routing, scheduling, and policies.
- **Channels**: ingress/egress adapters (Telegram, Discord, Slack, WhatsApp, etc.).
- **Agents**: model + policy + tool execution context.
- **Sessions**: conversation/routing identity and state.
- **Extensions**: browser/canvas/nodes/plugins expand capabilities outside plain chat.

## Runtime baseline
- Node.js 22+
- Typical setup: `openclaw onboard --install-daemon`
- Operational checks: `openclaw status`, `openclaw gateway status`, `openclaw channels status`
