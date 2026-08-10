# Troubleshooting Entry Points

Symptom-based routing to find the right troubleshooting guide.

## Quick diagnostics first

Before diving into specific issues, run the built-in diagnostics:

```bash
openclaw doctor
```

**This checks:**
- ✓ Node.js version (requires 22+)
- ✓ Config file validity
- ✓ Gateway status
- ✓ Channel connections
- ✓ Workspace permissions
- ✓ API key presence

**If `openclaw doctor` reports errors**, fix those first.

## Browse by symptom

### Installation and setup issues

**Problem: Installation fails**
- **Symptom:** `npm install -g openclaw` fails
- **Guide:** Check Node.js version (22+ required), permissions, network
- **See:** `100_DEVELOPMENT_SETUP.md` (if installing from source)

**Problem: Gateway won't start**
- **Symptom:** `openclaw gateway` exits immediately or hangs
- **Likely:** Config validation error, port in use, missing dependencies
- **Check:** `openclaw doctor`, `openclaw logs --tail 50`
- **See:** `111_COMMON_ERROR_PATTERNS.md`

**Problem: Config validation errors**
- **Symptom:** "Config validation failed" or Gateway refuses to start
- **Likely:** Syntax error, unknown key, wrong type in `openclaw.json`
- **Check:** `openclaw doctor --fix`, `openclaw config get <path>`
- **See:** `23_CONFIGURATION_SCHEMA_REFERENCE.md`

**Problem: Daemon not starting**
- **Symptom:** `systemctl --user start openclaw` fails
- **Likely:** User systemd not enabled, path issues, permissions
- **Check:** `journalctl --user -u openclaw`, `systemctl --user status openclaw`
- **See:** `81_SSH_AND_REMOTE_SETUP.md`

### Channel issues

**Problem: WhatsApp not connecting**
- **Symptom:** QR code scan fails, connection drops, status shows "Disconnected"
- **Likely:** QR code expired, network issue, WhatsApp server issues
- **Check:** `openclaw channels login whatsapp` (re-scan), check internet
- **See:** `31_CHANNEL_SETUP_CHECKLISTS.md`

**Problem: Telegram bot not receiving messages**
- **Symptom:** Bot doesn't respond, webhooks not firing
- **Likely:** Bot token incorrect, webhooks not set, bot blocked
- **Check:** `openclaw channels login telegram`, verify token
- **See:** `31_CHANNEL_SETUP_CHECKLISTS.md`

**Problem: Discord bot not responding**
- **Symptom:** Commands don't work, messages not received
- **Likely:** Bot token invalid, permissions missing, intents not enabled
- **Check:** Discord developer portal, bot token, server permissions
- **See:** `31_CHANNEL_SETUP_CHECKLISTS.md`

**Problem: Group chat mentions not working**
- **Symptom:** Bot doesn't respond when mentioned in groups
- **Likely:** `groupPolicy` not set to `requireMention`, mention pattern mismatch
- **Check:** `openclaw config get channels.<provider>.groups`
- **See:** `32_DM_AND_GROUP_POLICIES.md`

### Agent and model issues

**Problem: Agent not responding at all**
- **Symptom:** Message sent but no reply, timeout errors
- **Likely:** API key missing/invalid, model ID wrong, quota exceeded
- **Check:** `openclaw secret list`, `openclaw config get agents.defaults.model.primary`
- **See:** `41_MODEL_CONFIGURATION.md`

**Problem: Model API errors (401, 429, 500)**
- **Symptom:** "Unauthorized", "Rate limit exceeded", "Internal server error"
- **Likely:** Invalid API key, quota exceeded, provider outage
- **Check:** Provider dashboard, API key rotation, fallbacks
- **See:** `111_COMMON_ERROR_PATTERNS.md`

**Problem: Agent responses are slow**
- **Symptom:** Long delays between sending message and receiving reply
- **Likely:** Model latency, network issues, large context windows
- **Check:** Network speed, try faster model, reduce context
- **See:** `41_MODEL_CONFIGURATION.md`

**Problem: Agent makes mistakes or hallucinates**
- **Symptom:** Wrong information, incorrect tool usage
- **Likely:** Weak model, insufficient context, ambiguous prompt
- **Check:** Model strength, context injection, prompt clarity
- **See:** `42_THINKING_AND_REASONING_MODES.md`

### Tool execution issues

**Problem: `exec` commands failing**
- **Symptom:** "Command not found", permission denied, timeout
- **Likely:** Shell not found, missing permissions, command too long
- **Check:** `openclaw logs --tail 50`, verify shell path, check permissions
- **See:** `51_BUILT_IN_TOOLS_REFERENCE.md`

**Problem: Browser control not working**
- **Symptom:** Browser won't open, CDP connection failed
- **Likely:** Chrome not installed, remote browser not configured, WSL2 issue
- **Check:** `google-chrome --version`, check remote browser setup
- **See:** `53_BROWSER_CONTROL.md`, `docs/tools/browser-linux-troubleshooting.md`

**Problem: Elevated command approvals stuck**
- **Symptom:** Agent waiting for approval, `/approve` not working
- **Likely:** Approval mode misconfigured, wrong session, already approved
- **Check:** `openclaw sessions list`, verify approval mode
- **See:** `52_EXEC_SECURITY_AND_APPROVALS.md`

**Problem: Web search returns errors**
- **Symptom:** "API key missing", rate limit, empty results
- **Likely:** Brave API key not set, quota exceeded, search too broad
- **Check:** `openclaw secret get brave.search.apiKey`
- **See:** `51_BUILT_IN_TOOLS_REFERENCE.md`

### Session and memory issues

**Problem: Session context lost**
- **Symptom:** Agent doesn't remember previous messages
- **Likely:** Session expired, session ID mismatch, restart
- **Check:** `openclaw sessions list`, verify session key
- **See:** `21_SESSION_MODEL.md`

**Problem: MEMORY.md not loading**
- **Symptom:** Agent doesn't have access to long-term memory
- **Likely:** Not main session (groups, sub-agents don't load MEMORY.md)
- **Check:** Verify you're in DM, not group chat
- **See:** `44_MEMORY_AND_CONTEXT.md`

**Problem: Workspace files not accessible**
- **Symptom:** "File not found", permission denied
- **Likely:** Workspace path wrong, permissions issue, file doesn't exist
- **Check:** `openclaw config get agents.defaults.workspace`, verify path
- **See:** `91_PERMISSIONS_AND_SANDBOXING.md`

### Networking and remote access issues

**Problem: Can't access Gateway remotely**
- **Symptom:** `http://your-server:18789` not accessible from other device
- **Likely:** Firewall blocking port, Gateway not listening on public IP, NAT traversal
- **Check:** `ufw status`, `netstat -tlnp | grep 18789`
- **See:** `80_LOCALHOST_AND_PORTS.md`, `81_SSH_AND_REMOTE_SETUP.md`

**Problem: Tailscale node pairing fails**
- **Symptom:** Mobile device can't pair with Gateway over tailnet
- **Likely:** Not on same tailnet, Tailscale not running, port blocked
- **Check:** `tailscale status`, verify both devices on tailnet
- **See:** `82_TAILSCALE_AND_VPN_ACCESS.md`, `node-connect` skill

**Problem: SSH tunnel not working**
- **Symptom:** Can't access Gateway via SSH reverse tunnel
- **Likely:** SSH keys not set up, remote config wrong, port already in use
- **Check:** `ssh -R 18789:localhost:18789 user@server`, verify tunnel
- **See:** `81_SSH_AND_REMOTE_SETUP.md`

### Plugin and skill issues

**Problem: Plugin not loading**
- **Symptom:** "Plugin not found" or plugin features unavailable
- **Likely:** Not installed, path wrong, version incompatible
- **Check:** `npm list -g openclaw-skill-*`, verify installation
- **See:** `60_PLUGIN_ARCHITECTURE.md`

**Problem: Skill not triggering**
- **Symptom:** Agent doesn't follow skill workflow
- **Likely:** Skill not matched, conditional logic not met, skill outdated
- **Check:** Read skill's `SKILL.md`, verify trigger conditions
- **See:** `63_SKILLS_SYSTEM.md`

### Performance and resource issues

**Problem: Gateway consuming too much memory**
- **Symptom:** High memory usage (>500 MB), system slows down
- **Likely:** Large sessions, memory leak, many active sessions
- **Check:** `openclaw sessions list`, review session count
- **See:** `111_COMMON_ERROR_PATTERNS.md`

**Problem: Gateway consuming too much CPU**
- **Symptom:** High CPU usage when idle
- **Likely:** Background tasks, polling, hot-reload loops
- **Check:** `openclaw logs --tail 100`, look for loops
- **See:** `111_COMMON_ERROR_PATTERNS.md`

**Problem: Disk usage growing rapidly**
- **Symptom:** Workspace or logs directory growing large
- **Likely:** Log files not rotated, session data accumulation
- **Check:** `du -sh ~/.openclaw/`, `ls -lh ~/.openclaw/logs/`
- **See:** `93_AUDIT_AND_LOGGING.md`

## Log analysis workflow

When something goes wrong:

1. **Check Gateway logs:**
   ```bash
   openclaw logs --tail 100
   ```

2. **Check agent logs (if applicable):**
   ```bash
   openclaw logs --session <session-key> --tail 100
   ```

3. **Check channel logs:**
   ```bash
   openclaw logs --channel <provider> --tail 100
   ```

4. **Search for errors:**
   ```bash
   openclaw logs --tail 1000 | grep -i error
   ```

**See:** `112_LOG_ANALYSIS_GUIDE.md` for detailed log interpretation.

## When to escalate

Escalate to `113_ESCALATION_HANDOFF.md` when:

- Issue persists after following troubleshooting guides
- Error message not documented or unclear
- Suspected bug or regression
- Need help from community or maintainers

## Diagnostic data collection

Before escalating, gather:

1. **Version info:**
   ```bash
   openclaw --version
   ```

2. **Full doctor output:**
   ```bash
   openclaw doctor > doctor-output.txt
   ```

3. **Relevant logs:**
   ```bash
   openclaw logs --tail 500 > logs.txt
   ```

4. **Config (redact secrets):**
   ```bash
   openclaw config get --all > config.json
   # Remove API keys before sharing
   ```

## Provenance
- **Source:** docs/help/, docs/gateway/troubleshooting.md
- **Last validated:** 2026-03-18 (against openclaw@latest from GitHub)
