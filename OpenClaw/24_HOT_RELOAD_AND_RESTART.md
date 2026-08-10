# Hot Reload and Restart

## Scope
Config hot-reload behavior, daemon restarts, and state preservation rules. Covers what changes take effect immediately vs what requires a full Gateway restart.

## Audience
- Operators modifying OpenClaw configuration
- LLM systems advising on config change propagation

## Hot Reload vs Restart

| Action | Method | Downtime | When to Use |
|--------|--------|----------|-------------|
| **Hot reload** | `openclaw gateway restart` or SIGHUP | None (in-place) | Most config changes (credentials, agent settings, routing) |
| **Full restart** | `openclaw gateway stop && openclaw gateway start` | Brief (seconds) | Structural changes (ports, plugins, security mode) |
| **Process replacement** | Package update then restart | Brief | Version upgrades |

## Hot-Reloadable Settings

These changes take effect on next reload without dropping active sessions:

- **Agent configuration:** model, thinking level, permissions, tool allowlists
- **Channel credentials:** tokens, API keys, webhook URLs
- **Routing rules:** dmPolicy, groupPolicy, mention patterns
- **Plugin settings:** non-structural plugin config changes
- **Cron schedules:** job timing, payloads, delivery modes
- **Authorized senders:** allowlist additions and removals

## Restart-Required Settings

These require a full Gateway stop/start:

- **Listener ports:** `gateway.bind.port` changes
- **Plugin paths:** new plugin directories or plugin discovery changes
- **Security mode:** switching between security profiles
- **Database/persistence backend:** storage location or driver changes
- **Gateway bind address:** `gateway.bind.host` changes

## State Preservation

| State Type | Preserved on Hot Reload | Preserved on Full Restart |
|------------|------------------------|--------------------------|
| Active sessions | ✅ Yes | ❌ No (sessions reaped) |
| Session history | ✅ Yes | ✅ Yes (persisted to disk) |
| Sub-agent runs | ✅ Yes | ⚠️ May be orphaned |
| Pending approvals | ✅ Yes | ❌ No |
| Cron state | ✅ Yes | ✅ Yes (persisted) |
| Channel connections | ✅ Yes | ❌ Re-established |

## Commands

```bash
# Hot reload (preferred)
openclaw gateway restart

# Full restart
openclaw gateway stop
openclaw gateway start

# Check status
openclaw gateway status

# Verify config before reload
openclaw config validate
```

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| Config change not taking effect | Change requires full restart | Stop and start the Gateway |
| Sessions lost after restart | Expected behavior for full restart | Use hot reload for non-breaking changes |
| Plugin not loading after config edit | New plugin path requires restart | Full restart, then check logs |
| Hot reload fails silently | Config validation error | Run `openclaw config validate` first |

## Related Documentation

- `23_CONFIGURATION_SCHEMA_REFERENCE.md` — Config schema sections and examples
- `103_DIAGNOSTICS_AND_HEALTH.md` — Diagnostic data collection
- `120_VERSIONING_AND_CHANNELS.md` — Upgrade strategies

## Provenance

- Gateway reload logic from `packages/gateway/src/reload.ts`
- Config validation from `packages/config/`
- Official docs: <https://docs.openclaw.ai>
- Repository: <https://github.com/openclaw/openclaw>
