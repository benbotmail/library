# 06 — Security and Operations (Current Defaults)

## Security baseline
OpenClaw treats inbound channel traffic as untrusted. Core controls:
- Pairing / allowlists
- DM & group policies
- Gateway auth
- Tool restrictions / sandboxing
- Fail-closed defaults for sensitive routing

Primary references:
- `openclaw-src/docs/gateway/configuration-reference.md`
- `openclaw-src/docs/gateway/heartbeat.md`
- `openclaw-src/docs/channels/telegram.md`
- `openclaw-src/docs/channels/groups.md`

## Heartbeat defaults you must remember
From `docs/gateway/heartbeat.md`:
- Default cadence: `30m` (or `1h` for Anthropic OAuth/setup-token paths)
- Default target: `none` (run heartbeat turn without external delivery)
- Default `directPolicy`: `allow`
- `directPolicy: "block"` suppresses direct/DM heartbeat delivery while still running heartbeat logic
- Default prompt includes strict `HEARTBEAT_OK` ack contract

Key config block:
- `agents.defaults.heartbeat.*`
- per-agent overrides: `agents.list[].heartbeat.*`

## Channel security defaults (Telegram focus)
From `docs/channels/telegram.md` and config reference:
- `channels.telegram.dmPolicy`: default `pairing`
- `channels.telegram.groupPolicy`: default `allowlist`
- `channels.telegram.reactionNotifications`: default `own`
- `channels.telegram.reactionLevel`: default `minimal`
- `channels.telegram.replyToMode`: default `off`
- `channels.telegram.streaming`: default `off`

## Gateway/operator hygiene
1. Run `openclaw status` before changes
2. Use `openclaw doctor` for drift + migrations
3. Prefer least-destructive checks first (`status/list/--json`)
4. Keep explicit auth + bind config (avoid accidental broad exposure)
5. Validate post-change with channel probes/logs

## Incident triage order
1. Gateway alive/authenticated?
2. Channel connected?
3. DM/group policy + allowlists correct?
4. Mention gating/routing/session key expected?
5. Logs confirm drop reason vs delivery error?
