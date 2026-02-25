# 04 — Channels and Routing

## Channel docs location
- `openclaw-src/docs/channels/index.md`
- Per-channel pages under `openclaw-src/docs/channels/*.md`

Common channels documented: WhatsApp, Telegram, Discord, Slack, Google Chat, Signal, iMessage/BlueBubbles, Teams, Matrix, WebChat, others.

## Routing model (high-level)
- Channel account receives inbound messages.
- Gateway maps inbound context to agent/session.
- Group policies can require mentions / activation rules.
- Reply routing can target source channel/session context.

## Pairing and trust model
- Pairing + allowlists are central to DM safety for unknown senders.
- Approval flow is CLI-managed (`pairing`, `devices` surfaces).

## What bots should check first for channel bugs
1. Channel status (`openclaw channels status` / `openclaw status --deep`).
2. Channel-specific config in docs + current config file.
3. Pairing/allowlist/group mention settings.
4. Gateway logs (`openclaw logs`).
