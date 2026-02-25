# 05 — Tools and Automation

## Tooling areas to know
- Browser control: `openclaw-src/docs/tools/browser*` + CLI/browser docs
- Nodes/canvas/device actions: `openclaw-src/docs/nodes/*`, `openclaw-src/docs/platforms/*`
- Skills: `openclaw-src/docs/tools/skills*`
- Automation: `openclaw-src/docs/automation/*` (cron, webhooks, hooks, gmail pubsub)

## Automation primitives
- **Cron jobs** for scheduled execution.
- **System events** for internal wakeups/signals.
- **Webhooks** for external trigger intake.
- **Heartbeat patterns** for lightweight periodic checks.

## Bot workflow recommendation
For implementation tasks involving tools:
1. Confirm capability exists in docs first.
2. Confirm command/tool names exactly (avoid guessing).
3. Prefer deterministic, idempotent action patterns.
4. Add observability steps (status/logs) in runbooks.

## High-value doc pages to keep handy
- `docs/automation/cron-jobs.md`
- `docs/automation/webhook.md`
- `docs/automation/hooks.md`
- `docs/concepts/session-tool.md`
- `docs/tools/skills*.md`
