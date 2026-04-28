# OpenClaw Config Reference

> Auto-extracted from `schema.base.generated.ts` at commit `e84ebeafbd`.

## Streaming / Queue Modes (per channel)

All channels support the same queue mode values:

| Mode | Description |
|------|-------------|
| `steer` | Steer current turn |
| `followup` | Follow-up after current |
| `collect` | Collect into batch |
| `steer-backlog` | Steer with backlog |
| `steer+backlog` | Steer plus backlog |
| `queue` | Queue for next turn |
| `interrupt` | Interrupt current turn |

Configured via `agents.defaults.queueMode` (default) and `agents.defaults.queueModeByChannel` (per-channel override).

**Supported channels:** `telegram`, `discord`, `irc`, `slack`, `mattermost`, `signal`, `imessage`, `msteams`, `webchat`

Additional queue tuning:
- `debounceMs` / `debounceMsByChannel` — global and per-channel debounce in ms
- `cap` — max queued items before drop policy
- `drop` — drop policy when cap exceeded

## Heartbeat Config

Under `agents.defaults.heartbeat` (and per-agent `agents.list[].heartbeat`):

| Key | Type | Notes |
|-----|------|-------|
| `every` | string | Interval (e.g. `"30m"`) |
| `activeHours.start` | string | Start time |
| `activeHours.end` | string | End time |
| `activeHours.timezone` | string | Timezone |
| `model` | string | Model override |
| `session` | string | Session type |
| `includeReasoning` | boolean | |
| `target` | string | Delivery target |
| `directPolicy` | `"allow"` \| `"block"` | Whether heartbeat can DM (default: `allow`) |
| `to` | string | Recipient |
| `accountId` | string | Account selector |
| `prompt` | string | Custom heartbeat prompt |
| `includeSystemPromptSection` | boolean | Include heartbeat instructions in system prompt |
| `ackMaxChars` | integer | Max chars for ack |
| `suppressToolErrorWarnings` | boolean | |
| `timeoutSeconds` | integer | Per-heartbeat timeout |
| `lightContext` | boolean | |
| `isolatedSession` | boolean | |

## Exec / Security

Under `agents.defaults.exec`:

| Key | Values | Description |
|-----|--------|-------------|
| `host` | `auto`, `sandbox`, `gateway`, `node` | Execution target |
| `security` | `deny`, `allowlist`, `full` | Security posture |
| `ask` | `off`, `on-miss`, `always` | Approval strategy |
| `elevated` | boolean | Enable elevated exec path |
| `elevated.senders` | object[] | Sender allow rules for elevated tools |

## Channel Config

Under `agents.defaults`:
- `group.mentionPatterns` — regex patterns for trigger detection in groups
- `group.replyMode` — `"message_tool"` or `"automatic"`
- `inboundPrefix` / `outboundPrefix` — text prefixes on messages
- `silentReply.direct` — `"allow"` or `"disallow"`

## Agent Route Bindings

Bindings match on:
- `channel` (required) — provider ID (e.g. `telegram`, `discord`)
- `account` (optional) — multi-account selector
- `peer.kind` — `"direct"`, `"group"`, `"channel"`, `"dm"` (deprecated)
- `peer.id` — conversation identifier

DM session scope: `"per-channel-peer"`, `"per-account-channel-peer"`

## Update Channel

- `channel.id` — `"stable"`, `"beta"`, or `"dev"`
- `channel.minimumDelay` — hours before auto-apply
- `channel.rolloutSpread` — extra spread window in hours
- `channel.betaInterval` — how often beta checks run
