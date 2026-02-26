# 03 — CLI Surface Map

Primary source: `openclaw-src/docs/cli/index.md`

## Main command families
- Lifecycle/setup: `onboard`, `configure`, `doctor`, `update`
- Gateway/service: `gateway *`, `status`, `logs`, `health`
- Channels/messaging: `channels *`, `message *`, `pairing *`
- Agent/session: `agent`, `agents`, `sessions`, `acp`
- Automation: `cron *`, `system *`, `hooks`
- Devices/tools: `nodes *`, `browser *`, `plugins *`

## Safe operator flow
1. `openclaw status`
2. `openclaw gateway status`
3. targeted `... --help` or docs page
4. run read-only/list/status commands before mutating commands
