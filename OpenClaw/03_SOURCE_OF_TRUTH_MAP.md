# Source of Truth Map

> Authority order when claims conflict

## Authority Hierarchy (highest to lowest)

1. **Source code** — The runtime behavior of the Gateway is the ultimate truth. If documentation says X but the code does Y, the code wins.
2. **`openclaw config schema`** — The live JSON schema exported by the running Gateway. Reflects actual accepted keys and their types.
3. **`openclaw doctor`** — Diagnostic output from the running system. Reports actual state, detected misconfigurations, and applies fixes.
4. **Official docs** (`docs/` in the OpenClaw repo) — Curated prose documentation. May lag behind code between releases.
5. **Configuration reference** (`docs/gateway/configuration-reference.md`) — Field-level reference. May not cover every edge case or recently added key.
6. **Community resources** — Discord, GitHub discussions, blog posts. Useful for patterns but not authoritative.

## Practical Guidance

- When a config key's behavior is unclear, check `openclaw config schema` on a running Gateway for the canonical accepted shape.
- `openclaw doctor --fix` can detect and repair common config drift (retired reload modes, missing fields, etc.).
- The config schema lookup tool action is `config.schema.lookup` (via the `gateway` tool).
- Validation commands in the repo: `pnpm config:docs:check` and `pnpm config:docs:gen`.

## Key Source Files by Topic

| Topic | Primary source files |
|-------|---------------------|
| Configuration | `docs/gateway/configuration.md`, `docs/gateway/configuration-reference.md`, `docs/gateway/config-agents.md`, `docs/gateway/config-channels.md`, `docs/gateway/config-tools.md` |
| Telegram channel | `docs/channels/telegram.md`, `docs/plugins/reference/telegram.md` |
| Heartbeat | `docs/gateway/heartbeat.md`, `docs/automation/cron-vs-heartbeat.md` |
| Sandbox/Security | `docs/gateway/sandboxing.md`, `docs/gateway/sandbox-vs-tool-policy-vs-elevated.md`, `docs/tools/elevated.md` |
| Sessions | `docs/concepts/session.md`, `docs/concepts/main-session.md` |
| Cron | `docs/automation/cron-jobs.md`, `docs/automation/index.md` |
| Memory | `docs/concepts/memory.md`, `docs/reference/memory-config.md` |
| Tools | `docs/tools/index.md`, `docs/tools/elevated.md`, `docs/gateway/config-tools.md` |
| CLI | `docs/cli/openclaw.md`, individual `docs/cli/*.md` files |
| Hot reload | `docs/gateway/configuration.md` (§ Config hot reload) |
| Channels | `docs/channels/` directory, `docs/gateway/config-channels.md` |

## Conflicting Claims

When two sources disagree:
1. Check the source code behavior
2. Run `openclaw doctor` to see what the running system reports
3. Prefer the more specific/recent source
4. When docs say "default" but code has a different default, code wins
