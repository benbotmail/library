# Security Model

> Current as of 2026-08-19 (upstream `7a82d8b0f25`).

## Scope

Access control, sandboxing, tool policy, secret management, and isolation boundaries.

## Threat Model

| Threat | Mitigation |
|--------|------------|
| Unauthorized users sending commands | Sender allowlists + channel DM/group policies |
| Token/API key exposure | Environment variables, `tokenFile`, secret stores; no plaintext in config |
| Tool execution abuse | Approval flows, sandboxing, tool policy profiles, dangerous pattern detection |
| Session cross-contamination | Session isolation, per-agent workspaces, sandbox scopes |
| Privilege escalation via plugins | Plugin allowlists, sandbox tool policy gating |

## Authentication Layers

### 1. Channel Authentication
- **Bot tokens / phone numbers:** Platform-specific credentials
- **Stored in:** Environment variables, `tokenFile` paths, or `.secrets.baseline` (never in main config)
- **Token resolution priority:** `tokenFile` > `botToken` > env var (default account only)
- **Rotation:** Replace token in env/config, then `openclaw gateway restart`

### 2. Sender Authorization
- **`allowFrom`:** Per-channel list of authorized sender IDs (numeric Telegram IDs, E.164 phone numbers, Discord user IDs)
- **`dmPolicy`:** `pairing` (default) | `allowlist` | `open` | `disabled`
- **`groupPolicy`:** `allowlist` (default) | `open` | `disabled`
- **`groupAllowFrom`:** Restricts which users can trigger the bot within allowed groups
- Group sender auth never inherits DM pairing-store approvals (security boundary)

### 3. Device Pairing
- **Pairing codes:** Time-limited (1 hour) codes for initial DM access
- **Pairing flow:** User DMs bot → pairing code generated → operator approves
- First approved pairing also sets `commands.ownerAllowFrom` if no owner exists
- **Dashboard Mini App** (Telegram): verifies signed WebApp `initData` with bot token

### 4. Tool Execution Approvals
- **Elevated commands:** Require explicit `/approve` from operator
- **Approval is allow-once:** Each elevated command needs fresh approval
- **`tools.elevated`:** Lists tools allowed to bypass sandbox and run on host
- **`exec.security` modes:** `deny` | `allowlist` | `full`
- **Full-access sessions (`exec.security: "full"`) do not request exec approval** — the session's full-access policy propagates through compaction and session state; approval prompts only apply to sessions with stricter policies
- Dangerous commands trigger warnings before execution

### 5. Unattended Automation Surface
- **Automation triggers are ON by default** (`cron.triggers.enabled: true`): condition-trigger scripts, `script` payloads, and stream schedules run unattended with the owning agent's **full tool policy, including `exec`**
- Treat this as unattended code execution with agent permissions; set `cron.triggers.enabled: false` for a hard stop
- Secrets distinguish **protected (write-only)** values from **agent-readable** Gateway environment values — explicit access modes control what agents can read vs. what only the Gateway can inject

## Sandboxing

Sandboxing is **off by default**. Controlled by `agents.defaults.sandbox` (global) or `agents.entries.*.sandbox` (per-agent).

### Sandbox Settings

| Setting | Key | Values | Default |
|---------|-----|--------|---------|
| Mode | `sandbox.mode` | `off`, `non-main`, `all` | `off` |
| Scope | `sandbox.scope` | `agent`, `session`, `shared` | `agent` |
| Backend | `sandbox.backend` | `docker`, `podman`, `ssh`, `openshell` | `docker` |

**Mode controls when sandboxing applies:**
- `off`: No sandboxing
- `non-main`: Sandbox every session except the agent's main session
- `all`: Every session runs in a sandbox

**Scope controls container/environment sharing:**
- `agent`: One container per agent
- `session`: One container per session
- `shared`: One container for all sandboxed sessions

### What Gets Sandboxed
- Tool execution: `exec`, `read`, `write`, `edit`, `apply_patch`, `process`
- Optional sandboxed browser (`agents.defaults.sandbox.browser`)

### What Does NOT Get Sandboxed
- The Gateway process itself
- Tools explicitly in `tools.elevated` (bypass sandbox, run on host)

### Docker Backend Defaults
- `network: "none"` (no egress)
- `readOnlyRoot: true`
- `capDrop: ["ALL"]`
- Image: `openclaw-sandbox:bookworm-slim`
- Init process + `no-new-privileges`

```json5
{
  agents: {
    defaults: {
      sandbox: {
        mode: "all",
        backend: "docker",
        scope: "session",
        workspaceAccess: "ro",
        docker: {
          image: "openclaw-sandbox:bookworm-slim",
          readOnlyRoot: true,
          tmpfs: ["/tmp", "/var/tmp", "/run"],
          network: "none",
          capDrop: ["ALL"],
        },
      },
    },
  },
}
```

### Podman Backend
- Uses same `sandbox.docker.*` settings
- Rootless defaults to `--userns=keep-id` for writable workspace mounts
- Browser sandboxing not supported

### SSH Backend
- Run tools on any SSH-accessible machine
- Workspace model: seed once, remote-canonical

### OpenShell Backend
- Managed remote sandboxes via OpenShell plugin
- `mirror` or `remote` workspace modes

### Sandboxed Browser
- Auto-starts in separate browser container (Docker backend only)
- Dedicated Docker network (`openclaw-sandbox-browser`)
- `cdpSourceRange` restricts CDP ingress
- noVNC observer access password-protected

## Tool Policy

### Profiles

| Profile | Tools |
|---------|-------|
| `minimal` | `session_status` only |
| `coding` | `group:fs`, `group:runtime`, `group:sessions`, `group:memory`, `image` |
| `messaging` | `group:messaging`, `sessions_*`, `session_status` |
| `full` | All tools (default) |

### Tool Policy Layers

Tool policy applies as intersecting layers — a deny in any layer blocks the tool:

1. Global `tools.deny` / `tools.allow`
2. Per-agent `agents.entries.*.tools`
3. Per-channel `channels.<channel>.direct.<chatId>.tools`
4. Per-sender `toolsBySender`
5. Sandbox tool policy (`tools.sandbox.tools`)

### `toolsBySender`

Selects a sender-specific policy by typed sender key:

```json5
{
  channels: {
    telegram: {
      direct: {
        "*": { tools: { deny: ["write", "edit"] } },
        "603767951": { tools: {} },
      },
    },
  },
}
```

### Elevated Tools

`tools.elevated` lists tools that bypass sandbox and run on the host escape path. If sandboxing is off, `tools.elevated` has no effect.

## Session Isolation

- Each session has independent message history and context
- Sub-agent sessions inherit workspace but have their own execution context
- Memory files (`MEMORY.md`) only loaded in main sessions, not shared contexts
- Group sessions isolated by group ID; forum topics append `:topic:<threadId>`

## Secret Management

### Sentinel-based egress protection

Model-provider credentials backed by SecretRefs are minted as opaque, process-local sentinels (`oc-sent-v2.<authenticated-ciphertext>.end`). Logs, error objects, and runtime introspection never see the plaintext; substitution happens immediately before each request leaves the process. Unknown sentinel-shaped values **fail closed** — the request is refused rather than forwarded. Resolved values are also exact-match log-redacted.

### Secret egress proxy (default-off)

Lets Gateway-hosted agent subprocesses use shared-store `secret` entries without receiving plaintext:

- Enable via `secrets.egressProxy.enabled: true` (requires Gateway restart); configures `HTTPS_PROXY`/`HTTP_PROXY` to a loopback proxy plus an ephemeral CA (`NODE_EXTRA_CA_CERTS`, etc.) in exec environments.
- Each secret must name exact allowed HTTPS hosts: `openclaw secrets store set NAME --allow-host api.example.com` (repeatable; `--clear-allowed-hosts` removes). Wildcards/suffixes/ports unsupported; unbound secrets are never substituted; refused requests print the exact fix command.
- Proxy auth: Basic auth (`openclaw` + per-run random password), expires when the agent run closes; `407` on missing/expired credentials.
- `bypassHosts`: authenticated blind CONNECT tunnels for certificate-pinned clients — sentinels are not substituted there (fail vendor auth safely).
- Limits: HTTP/1.1 upstream only; no HTTP/2, WebSocket rewriting, or plain-HTTP substitution; applies only to **Gateway-hosted exec** (sandbox/remote node exec and provider-native harnesses are excluded); hostname-based policy is not an IP pin.

| Secret Type | Storage | Example |
|-------------|---------|---------|
| API keys/tokens | Environment variables | `TELEGRAM_BOT_TOKEN`, `OPENAI_API_KEY` |
| Config secrets | `.secrets.baseline` or `tokenFile` | Channel credentials |
| Session data | Internal SQLite database | Session history, state |
| Pairing tokens | Internal store | Device pairing |

### Best Practices
- Never commit secrets to version control
- Use `tokenFile` pointing to a permission-restricted file (`chmod 600`)
- Rotate tokens periodically; update env/file then restart Gateway
- Use separate tokens for dev and production

## Common Misconfigurations

| Misconfiguration | Risk | Fix |
|------------------|------|-----|
| `dmPolicy: "open"` on public bot | Anyone can issue commands | Use `allowlist` with numeric IDs |
| Tokens in main config file | Accidental commit/exposure | Move to env vars or `tokenFile` |
| No sandbox in shared environments | Unrestricted tool execution | Enable `sandbox.mode: "non-main"` or `"all"` |
| Gateway exposed on 0.0.0.0 without TLS | Network interception | Bind to loopback, use Tailscale/tunnel |
| `groupAllowFrom` with group chat IDs | Bot ignores group messages | Put group IDs under `channels.<channel>.groups` |
| Sandboxed Codex with DooD | Path mapping failures | Use host-absolute paths in `workspace` config |

## Related Documentation

- [Sandboxing](https://docs.openclaw.ai/gateway/sandboxing)
- [Configuration](https://docs.openclaw.ai/gateway/configuration)
- [Telegram Channel](https://docs.openclaw.ai/channels/telegram)
- [Elevated Mode](https://docs.openclaw.ai/tools/elevated)
