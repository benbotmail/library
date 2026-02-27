# 04 — Channels and Routing (Current Behavior)

Primary refs:
- `openclaw-src/docs/channels/telegram.md`
- `openclaw-src/docs/channels/group-messages.md`
- `openclaw-src/docs/gateway/configuration-reference.md` (Channels)

## Global defaults to remember
- DM policy default: `pairing`
- Group policy default: `allowlist`
- If `channels.<provider>` block is missing entirely, group handling is fail-closed (`allowlist` fallback).

## Mention gating vs access policy
- `groupPolicy` controls whether group traffic is eligible.
- `requireMention` controls whether eligible traffic triggers a response.
- These are separate gates and both can block replies.

## Telegram current-state behavior
- Production-ready; long polling default, webhook optional.
- `channels.telegram.dmPolicy`: `pairing | allowlist | open | disabled` (default `pairing`).
- `channels.telegram.groupPolicy`: `open | allowlist | disabled` (default `allowlist`).
- Group/topic routing is isolated by session key (`group:<chatId>` and `:topic:<threadId>`).
- `groupAllowFrom` is sender filter for group execution; if omitted, fallback can use `allowFrom`.

## Telegram streaming key (canonical)
Use `channels.telegram.streaming`.

Allowed values:
- `off` (default)
- `partial`
- `block`
- `progress` (compat alias; mapped to partial behavior on Telegram)

Legacy `streamMode` and boolean `streaming` forms are auto-migrated, but new config should use the canonical key/value set above.

## Routing incident checklist
1. `openclaw status --deep`
2. `openclaw channels status --probe`
3. verify `dmPolicy/groupPolicy/allowFrom/groupAllowFrom/groups`
4. verify mention settings (`requireMention`, `/activation` state)
5. inspect `openclaw logs --follow` for explicit drop reasons
