# 05 — Tools and Automation

## Tooling areas
- Built-ins: filesystem/runtime/web/browser/canvas/nodes/messaging/session tools
- Skills: task-specific instruction bundles
- Automations: cron, hooks/webhooks, heartbeat/system events

Primary references:
- `openclaw-src/docs/tools/index.md`
- `openclaw-src/docs/automation/*`
- `openclaw-src/docs/gateway/heartbeat.md`

## Automation guidance
- Use **cron** for precise timing and isolated jobs.
- Use **heartbeat** for periodic assistant check-ins/stateful proactive behavior.
- Keep heartbeat prompt/checklist small (`HEARTBEAT.md`) to control token cost.

## Important heartbeat config keys
- `agents.defaults.heartbeat.every`
- `agents.defaults.heartbeat.target`
- `agents.defaults.heartbeat.directPolicy`
- `agents.defaults.heartbeat.prompt`
