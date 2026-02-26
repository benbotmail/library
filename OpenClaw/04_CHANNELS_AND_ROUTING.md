# 04 — Channels and Routing (Current Behavior)

## Channel model
OpenClaw routes inbound channel events to session keys, then replies back through the same channel context.

Primary references:
- `openclaw-src/docs/channels/groups.md`
- `openclaw-src/docs/channels/telegram.md`
- `openclaw-src/docs/gateway/configuration-reference.md` (Channels section)

## DM and group defaults (important)
- **DM policy default:** `pairing`
- **Group policy default:** `allowlist`
- If a provider config block is entirely missing, runtime uses fail-closed group behavior (allowlist-like), not permissive open routing.

## Mention gating
- Group replies are usually mention-gated by default (`requireMention: true` patterns are common defaults).
- Reply-to-bot-message often counts as implicit mention on supported platforms.
- Mention behavior is separate from `groupPolicy`.

## Telegram-specific routing highlights
From `docs/channels/telegram.md`:
- Telegram is production-ready with **long polling default**; webhook mode optional.
- `dmPolicy` values: `pairing | allowlist | open | disabled` (default `pairing`).
- `groupPolicy` values: `open | allowlist | disabled` (default `allowlist`).
- Group/topic session isolation:
  - group: `agent:<id>:telegram:group:<chatId>`
  - forum topic adds `:topic:<threadId>`
- `groupAllowFrom` applies sender filtering in groups; fallback can use `allowFrom` when unset.

## Telegram streaming modes (current key)
Canonical key: `channels.telegram.streaming`
- Allowed: `off | partial | block | progress`
- Default: `off`
- `progress` is compatibility-mapped to `partial` on Telegram.
- Legacy `streamMode`/boolean forms are auto-mapped, but new docs/config should use `streaming`.

## Practical routing checks for incidents
1. `openclaw status --deep`
2. `openclaw channels status`
3. Validate per-channel `dmPolicy/groupPolicy/allowFrom/groupAllowFrom/groups`
4. Check mention gating (`requireMention`, `/activation` where supported)
5. Review `openclaw logs --follow`
