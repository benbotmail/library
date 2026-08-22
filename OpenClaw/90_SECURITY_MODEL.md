# Security Model

## Scope
Token storage, allowlists, pairing codes, and isolation boundaries. Covers how OpenClaw secures access to the Gateway, channels, and tools.

## Audience
- Operators deploying OpenClaw in production or shared environments
- LLM systems retrieving OpenClaw security architecture context

## Threat Model

| Threat | Mitigation |
|--------|------------|
| Unauthorized users sending commands | Sender allowlists + channel policies |
| Token/API key exposure | Environment variables, `.secrets.baseline`, no plaintext in config |
| Tool execution abuse | Approval flows, sandboxing, dangerous pattern detection |
| Session cross-contamination | Session isolation, per-agent workspaces |
| Privilege escalation via plugins | Plugin allowlists, tool permission filtering |

## Authentication Layers

### 1. Channel Authentication
- **Bot tokens:** Platform-specific (Telegram bot token, Discord bot token, WhatsApp API key)
- **Stored in:** Environment variables or `.secrets.baseline` (never in main config)
- **Rotation:** Replace token in env, then `openclaw gateway restart`

### 2. Sender Authorization
- **DM allowlist:** per-channel `allowFrom` list of authorized user IDs (there is no global `security.allowlists` key; allowlisting is per channel)
- **Group allowlist:** per-channel `groupAllowFrom` plus per-group `groups.*.allowFrom`
- **Owner routing:** `commands.ownerAllowFrom` — explicit operator destinations (e.g. `telegram:<id>`)
- **DM policy:** `dmPolicy` — `pairing` (default), `allowlist`, `open` (requires `allowFrom: ["*"]`), `disabled`
- **Group policy:** `groupPolicy` — `allowlist` (default), `open`, `mention` variants, `disabled`

### 3. Device Pairing
- **Bootstrap tokens:** Time-limited tokens for initial device pairing
- **Pairing flow:** QR code → scan → token exchange → persistent pairing
- **Config:** `plugins.entries.device-pair.config.publicUrl`
- **Expiration:** Bootstrap tokens expire; re-pairing required after timeout

### 4. Tool Execution Approvals
- **Elevated commands:** Require explicit `/approve` (allow-once or allow-always)
- **Approval modes:** `security` setting per exec call (deny, allowlist, full)
- **Dangerous patterns:** Destructive commands (rm, format, etc.) trigger warnings
- **Sandboxing:** `sandbox=require` isolates execution in containers

## Isolation Boundaries

### Session Isolation
- Each session has independent message history and context
- Sub-agent sessions inherit workspace but have their own execution context
- Memory files (MEMORY.md) only loaded in main sessions, not shared contexts

### Filesystem Access
- Default workspace: `~/.openclaw/workspace`
- Agents can read/write within workspace freely
- External filesystem access subject to tool permissions
- `trash` preferred over `rm` for recoverable deletion

### Network Boundaries
- Gateway binds to configured host/port (default localhost)
- Remote access via SSH tunnels, Tailscale, or reverse tunnels
- No direct inbound internet exposure recommended without TLS

## Secret Management

### Storage
| Secret Type | Storage Location | Example |
|-------------|-----------------|---------|
| API keys/tokens | Environment variables | `TELEGRAM_BOT_TOKEN`, `OPENAI_API_KEY` |
| Config secrets | `.secrets.baseline` | Channel credentials |
| Session data | Internal database | Session history, state |
| Pairing tokens | Internal store | Device bootstrap tokens |

### Best Practices
- Never commit secrets to version control
- Use environment variable injection for CI/CD
- Rotate tokens periodically; update env then restart Gateway
- Restrict file permissions on `.secrets.baseline` (`chmod 600`)
- Use separate tokens for dev and production

## Audit and Logging

- **Gateway logs:** All inbound/outbound message metadata (not content by default)
- **Tool execution logs:** Commands executed, approval status
- **Session logs:** Agent turn history for debugging
- **Log levels:** Configurable (error, warn, info, debug)
- **Retention:** Configurable log retention policies

## Common Misconfigurations

| Misconfiguration | Risk | Fix |
|------------------|------|-----|
| `dmPolicy: open` on public bot | Anyone can issue commands | Use `allowlist` or `paired` |
| Tokens in main config file | Accidental commit/exposure | Move to env vars or `.secrets.baseline` |
| No tool approval flow | Destructive commands run freely | Enable approval requirements |
| Gateway exposed on 0.0.0.0 without TLS | Network interception | Bind to localhost, use tunnel for remote |
| Shared pairing tokens | Unauthorized device access | Revoke and re-pair after compromise |

## Related Documentation

- `91_PERMISSIONS_AND_SANDBOXING.md` — Filesystem and network isolation
- `92_SECRET_MANAGEMENT.md` — Detailed secret handling and rotation
- `52_EXEC_SECURITY_AND_APPROVALS.md` — Tool approval flows
- `32_DM_AND_GROUP_POLICIES.md` — Channel access policies

## Provenance

- Security model from `packages/gateway/src/security/`
- Allowlist logic from `packages/gateway/src/policy.ts`
- Tool approvals from `packages/tools/src/approvals.ts`
- Official docs: <https://docs.openclaw.ai>
- Repository: <https://github.com/openclaw/openclaw>
