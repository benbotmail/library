# OpenClaw Troubleshooting Guide

> Common issues, diagnostics, and fixes

## Diagnostic Commands

Run in order:

```bash
openclaw status                    # System status
openclaw gateway status            # Gateway status
openclaw gateway status --deep     # Deep probe
openclaw logs --follow             # Live logs
openclaw doctor                    # Health check
openclaw channels status --probe   # Channel connectivity
```

Healthy signals:
- `Runtime: running`
- `RPC probe: ok`
- No blocking issues from `doctor`

---

## Gateway Issues

### Gateway won't start

```bash
openclaw doctor
openclaw doctor --fix
```

Check for:
- Config validation errors
- Port conflicts (default 18789)
- Missing Node.js (v22+)

### No replies from agent

```bash
openclaw pairing list <channel>
openclaw config get channels
openclaw logs --follow
```

Look for:
- `pairing request` → Sender needs approval
- `drop guild message (mention required)` → Group mention gating
- `blocked` / `allowlist` → Policy filter

### Dashboard won't connect

```bash
openclaw gateway status --json
```

Check:
- URL matches (http://127.0.0.1:18789)
- Auth token configured
- Secure context for device identity

---

## Channel Issues

### WhatsApp

```bash
openclaw channels login --channel whatsapp
openclaw channels status --probe
```

Common issues:
- QR code expired → Re-run `channels login`
- Session disconnected → Re-link
- Phone number not allowed → Check `allowFrom`

### Telegram

```bash
openclaw pairing list telegram
openclaw config get channels.telegram.botToken
```

Common issues:
- Bot token invalid → Re-create in BotFather
- Privacy mode blocking group messages → Disable with `/setprivacy`
- Sender not approved → Check `dmPolicy` / `allowFrom`

### Discord

```bash
openclaw channels status discord --probe
```

Common issues:
- Bot not in guild → Invite bot
- Missing permissions → Check bot roles
- Mention required → Check `groups.*.requireMention`

### Slack

```bash
openclaw channels login slack
```

Common issues:
- Invalid tokens → Re-login
- App not installed → Reinstall app
- Socket mode issues → Check `appToken`

---

## Session Issues

### Context too long

```bash
# In chat:
/compact
/new
```

### Session not resetting

```bash
openclaw config get session.reset
```

Check:
- `mode` is set correctly
- `idleMinutes` configured
- `/new` trigger works

### Cross-user context leakage

```bash
openclaw config get session.dmScope
```

Fix: Set `dmScope: "per-channel-peer"` for multi-user

---

## Tool Issues

### Exec commands blocked

```bash
openclaw config get tools.deny
```

Check:
- `exec` not in deny list
- Sandbox permissions

### Browser not working

```bash
openclaw browser status
openclaw browser start
```

Common issues:
- `browser.enabled: false` → Enable in config
- CDP port conflict → Check port 18800+
- SSRF blocking → Check `ssrfPolicy`

### Web search failing

```bash
openclaw config get plugins.entries.brave
```

Check:
- API key configured
- `tools.web.search.enabled: true`

---

## Performance Issues

### Slow responses

1. Check model latency
2. Enable fallbacks
3. Reduce context window usage

```bash
openclaw config get agents.defaults.model
```

### High memory usage

```bash
openclaw sessions cleanup --dry-run
openclaw sessions cleanup --enforce
```

Enable maintenance:
```json5
{
  session: {
    maintenance: {
      mode: "enforce",
      pruneAfter: "14d",
      maxEntries: 200,
    },
  },
}
```

---

## Auth Issues

### OAuth tokens expired

```bash
openclaw doctor
openclaw login --provider anthropic
```

### Device pairing stuck

```bash
openclaw devices list
openclaw devices approve <requestId>
```

---

## Recovery

### Reset config

```bash
mv ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.bak
openclaw configure
```

### Clear sessions

```bash
rm -rf ~/.openclaw/agents/*/sessions/*
```

### Full reset

```bash
rm -rf ~/.openclaw
openclaw onboard
```

---

## Log Analysis

```bash
# Live logs
openclaw logs --follow

# Recent errors
openclaw logs | grep -i error

# Channel-specific
openclaw logs | grep -i telegram
```

---

## Getting Help

1. Run `openclaw doctor --deep`
2. Check logs: `openclaw logs --follow`
3. Search docs: https://docs.openclaw.ai
4. Discord: https://discord.gg/clawd
5. GitHub issues: https://github.com/openclaw/openclaw/issues
