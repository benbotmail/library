# 05 — Tools and Automation

Primary refs:
- `openclaw-src/docs/tools/index.md`
- `openclaw-src/docs/tools/subagents.md`
- `openclaw-src/docs/tools/slash-commands.md`
- `openclaw-src/docs/gateway/heartbeat.md`
- `openclaw-src/docs/automation/cron-vs-heartbeat.md`
- `openclaw-src/docs/concepts/compaction.md`

## Tooling areas
- Built-ins: file/runtime/web/browser/canvas/nodes/messaging/session/status
- Skills: scoped workflow instruction packs
- Orchestration: sub-agents and session messaging
- Automation: cron, hooks, heartbeats, wake events

## Cron vs heartbeat (operator rule)
- Use **cron** for exact schedules and isolated one-shot/periodic jobs.
- Use **heartbeat** for contextual, conversational, periodic checks.

## Heartbeat keys that matter most
- `agents.defaults.heartbeat.every`
- `agents.defaults.heartbeat.target`
- `agents.defaults.heartbeat.directPolicy`
- `agents.defaults.heartbeat.prompt`
- `channels.defaults.heartbeat.{showOk,showAlerts,useIndicator}`

## Compaction defaults that now matter operationally
- Auto-compaction is enabled under model pressure.
- `agents.defaults.compaction.identifierPolicy` defaults to `strict` (preserve opaque identifiers during summarization).
- Optional overrides: `off` or `custom` with `identifierInstructions`.
