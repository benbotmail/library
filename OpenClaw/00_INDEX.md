# OpenClaw Docs Pack (Current-State, LLM-Ready)

This pack summarizes **how OpenClaw works now** (not changelog narrative).

- Upstream source repo: `openclaw-src`
- Upstream commit processed: `1d43202930255eefc95527fdfdaaa3d0c867d054`
- Refresh date (UTC): 2026-02-26

## Read order
1. `01_PROJECT_OVERVIEW.md`
2. `02_ARCHITECTURE_AND_RUNTIME.md`
3. `03_CLI_SURFACE_MAP.md`
4. `04_CHANNELS_AND_ROUTING.md`
5. `05_TOOLS_AND_AUTOMATION.md`
6. `06_SECURITY_AND_OPERATIONS.md`
7. `07_AGENT_WORKSPACE_MEMORY_SKILLS.md`
8. `08_QUICK_TASK_ROUTER.md`

## Canonical upstream docs to trust first
- `openclaw-src/docs/index.md`
- `openclaw-src/docs/gateway/configuration-reference.md`
- `openclaw-src/docs/channels/telegram.md`
- `openclaw-src/docs/gateway/heartbeat.md`
- `openclaw-src/docs/channels/groups.md`

## Notes for assistant workflows
- Prefer local docs under `openclaw-src/docs/*` before web copies.
- Treat this pack as a map; verify exact keys/defaults in upstream docs before making config changes.
- For channel + safety behavior, always cross-check `04_*` and `06_*`.
