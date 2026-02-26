# 01 — Project Overview

OpenClaw is a self-hosted assistant gateway: channels in, agent/tool runtime in the middle, replies/actions out.

Current references:
- `openclaw-src/README.md`
- `openclaw-src/docs/index.md`
- `openclaw-src/docs/start/getting-started.md`

## Core model
- Gateway is the control plane.
- Channels are ingress/egress surfaces.
- Agents execute turns with tools.
- Sessions hold routing + conversation state.
- Nodes/canvas/browser extend execution beyond chat.

## Runtime baseline
- Node.js 22+
- Typical setup: `openclaw onboard --install-daemon`
- Health checks: `openclaw status`, `openclaw gateway status`
