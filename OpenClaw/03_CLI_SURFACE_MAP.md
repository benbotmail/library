# 03 — CLI Surface Map

Primary source: `openclaw-src/docs/cli/index.md`

## Core command families
- Setup/lifecycle: `setup`, `onboard`, `configure`, `doctor`, `update`
- Gateway ops: `gateway *`, `status`, `health`, `logs`
- Agent interactions: `agent`, `agents`, `sessions`, `acp`
- Messaging: `message *`, channel helpers
- Automation: `cron *`, `hooks`, `webhooks`
- Infra/device: `nodes *`, `node *`, `devices *`, `browser *`
- Config/model/security: `config *`, `models *`, `security audit`, `approvals`

## Practical operator shortcuts
- Check status: `openclaw status`
- Check gateway service: `openclaw gateway status`
- Start gateway service: `openclaw gateway start`
- Open docs index: `openclaw docs`

## Bot guidance
When given an OpenClaw CLI task:
1. Locate command family in this file.
2. Validate exact syntax in `openclaw-src/docs/cli/<command>.md`.
3. Prefer least-destructive subcommands first (`status`, `list`, `--json`).
