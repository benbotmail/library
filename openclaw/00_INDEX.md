# OpenClaw Docs Pack (Current-State, LLM-Ready)

This pack summarizes **current OpenClaw behavior** (not changelog narration).

- Upstream source repo: `openclaw-src`
- Upstream commit processed: `f212351aeddc38e0777baf29ff849cf427e4af5e`
- Refresh date (UTC): 2026-03-03

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
- `openclaw-src/docs/gateway/security/index.md`
- `openclaw-src/docs/channels/channel-routing.md`

## Usage notes
- Prefer local docs in `openclaw-src/docs/*` before any mirror.
- Treat this pack as a fast map for LLM context loading.
- For routing/safety decisions, always cross-check `04_*` and `06_*`.
