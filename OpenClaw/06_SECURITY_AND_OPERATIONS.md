# 06 — Security and Operations (Current Defaults)

Primary refs:
- `openclaw-src/docs/gateway/security/index.md`
- `openclaw-src/docs/gateway/configuration-reference.md`
- `openclaw-src/docs/gateway/heartbeat.md`
- `openclaw-src/docs/channels/telegram.md`

## Core security posture
OpenClaw treats inbound channel traffic as untrusted by default.

Primary controls:
- DM policy + allowlists/pairing
- Group policy + group allowlists + mention gating
- Gateway auth + explicit exposure controls
- Tool security modes/sandbox/workspace restrictions
- Fail-closed group fallback when provider config is missing

## Heartbeat defaults (important)
- cadence default: `30m` (or `1h` for Anthropic OAuth/setup-token mode)
- `target` default: `none`
- `directPolicy` default: `allow`
- `directPolicy: "block"` prevents direct/DM heartbeat delivery but still runs the turn
- `HEARTBEAT_OK` is treated as an ack contract in heartbeat context

## Telegram defaults relevant to security/routing
- `channels.telegram.dmPolicy`: `pairing` (default)
- `channels.telegram.groupPolicy`: `allowlist` (default)
- `channels.telegram.streaming`: `off` (default)
- canonical streaming values: `off | partial | block | progress` (`progress` maps to partial behavior)
- legacy `streamMode`/boolean `streaming` forms are compatibility-migrated; canonical key is `channels.telegram.streaming`
- `channels.telegram.replyToMode`: `off` (default)
- `channels.telegram.reactionNotifications`: `own` (default)

## Operator hygiene
1. `openclaw status` before changes
2. `openclaw doctor` for migration/drift fixes
3. prefer read-only diagnostics first
4. keep explicit channel/access settings (avoid accidental opens)
5. validate behavior with logs and channel probes after edits
