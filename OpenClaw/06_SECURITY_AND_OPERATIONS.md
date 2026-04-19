---
summary: "Security model, credential handling, SSRF, exec approvals, and operational practices"
read_when:
  - Setting up credentials or auth
  - Configuring security policies
  - Understanding SSRF, exec approvals, or send policy
title: "Security and Operations"
---

# Security and Operations

## Credential Management

### SecretRef
Credentials use SecretRef objects that support multiple resolution paths:
- Environment variables: `{ env: "API_KEY" }`
- File-based: `{ file: "/path/to/secret" }` (regular files only; symlinks rejected)
- Inline: `"plaintext-string"`
- Exec-based: `{ exec: "command" }` for dynamic credential resolution

Bot tokens accept SecretRef: e.g., `channels.discord.token` supports env/file/exec providers.

### Auth Profiles
- Stored in `~/.openclaw/auth-profiles/`
- Runtime snapshots capture current auth state
- Auth profile rotation supported for embedded agents
- OAuth manager handles concurrent agent auth
- OAuth hardening for Codex CLI bridge

### Provider Auth
- Provider env vars dynamically resolved
- API keys from `models.providers.<id>.apiKey` or env vars
- Google Gemini: dedicated plugin transport with its own auth
- Google Gemini CLI: unofficial OAuth flow via local `gemini` install
- Copilot Proxy: local VS Code Copilot Proxy bridge
- Codex provider: API key in catalog for models.json loading

### Credential Rotation
- HTTP auth re-resolved per-request to honor credential rotation
- SecretRef inspect/strict behavior aligned across preload and runtime paths
- Config snapshots redact `sourceConfig` and `runtimeConfig` alias fields

## SSRF Protection

### Browser SSRF
- Enforced on snapshot, screenshot, navigation, and tab routes
- Default hostname SSRF relaxed for loopback connections
- `browser.ssrfPolicy.dangerouslyAllowPrivateNetwork`: opt-in for private network access
- `browser.ssrfPolicy.hostnameAllowlist`: allow specific hostnames
- `browser.ssrfPolicy.allowedHostnames`: allow specific host patterns
- Remote CDP endpoint discovery also checked in strict mode
- Managed loopback CDP startup and control unblocked under strict defaults

### MCP SSRF
- Loopback request validation hardened
- Media paths validated against local roots

### Media Path Security
- `localRoots` containment enforced on webchat audio embedding
- Remote-host `file://` URLs rejected in media embedding
- Media source URLs validated
- Fail-closed on attachment canonicalization failures

## Exec Approvals

### How It Works
1. Agent requests exec command
2. Gateway checks approval policy
3. If approval required, prompt sent to configured approvers
4. Approver approves/denies via button or CLI (`/approve allow-once|allow-always|deny`)

### Channel-Native Delivery
- **Discord**: `execApprovals.target` → `dm`, `channel`, or `both`
- **Slack**: Same schema as Discord
- Approval prompts **redact secrets** (sourceConfig, runtimeConfig)

### Config
```json5
{
  channels: {
    discord: {
      execApprovals: {
        enabled: "auto",  // true | false | "auto"
        approvers: ["userId"],
        agentFilter: ["default"],
        sessionFilter: ["discord:"],
        target: "dm",
        cleanupAfterResolve: false,
      },
    },
  },
}
```

### Elevated Exec
- `elevated: true` escapes sandbox onto configured host path
- `security=full` forced when elevated resolves to `full`
- `strictInlineEval` forces approval for inline interpreter eval forms
- `safeBins` for stdin-only binaries that bypass allowlist

## SendPolicy

Controls outbound delivery behavior:
```json5
{
  channels: {
    telegram: {
      sendPolicy: {
        deny: [
          { channel: "telegram", chatType: "group" },
        ],
      },
    },
  },
}
```

**Key behavior**: `sendPolicy` deny suppresses **delivery**, not inbound processing. The agent still runs; only the outbound reply is blocked.

## Config Mutation Guards

Dangerous gateway config mutations are guarded:
- `tools.exec.ask` and `tools.exec.security` are protected from config writes
- Legacy `tools.bash.*` aliases that normalize to those paths are also protected
- `configWrites: false` blocks platform-initiated config writes

## Config Safety
- Strict schema validation; gateway refuses to start on unknown/invalid keys
- Best-effort config loading for resilience
- Config hash re-read after persist to avoid stale-hash race
- Legacy config migrations with fast-path for bundled channels

## Operational Notes

### Logging
- Failover log includes source and target model
- Diagnostic events track session state
- Console capture for specific subsystems

### Service Management
- systemd: unit files avoid inline dotenv secrets during service repair
- launchd: macOS service management
- PID-based restart for stale processes
- Graceful shutdown with close timeout handle cleanup

### Error Classification
- `finish_reason: network_error` classified as timeout for failover
- Invalid-model errors trigger fallback
- `connection-mismatch` replay errors classified as replay-invalid
- `No conversation found` classified as session_expired

### Resilience
- Inbound deduplication across restarts (Discord, BlueBubbles, WhatsApp)
- Reply queue splits batches by auth context
- Outbound delivery queue recovery for transient failures
- Tool loop detection prevents infinite retries
- Context engine graceful degradation on plugin resolution failure

### CI & Release
- Cross-OS release checks
- Live and E2E test checks
- QA lab provider framework restructured