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

Bot tokens accept SecretRef: `channels.telegram.botToken` or `channels.telegram.tokenFile` with `TELEGRAM_BOT_TOKEN` as env fallback.

### Auth Profiles
- Stored in `~/.openclaw/auth-profiles/`
- Path constants: `path-constants.ts` defines all auth profile paths
- Source checking validates auth profile availability
- Runtime snapshots capture current auth state
- Auth profile rotation supported for embedded agents

### Provider Auth
- Provider env vars dynamically resolved
- API keys from `models.providers.<id>.apiKey` or env vars
- Google Copilot: dedicated auth flow for embedding access
- Codex provider: API key included in catalog for models.json loading

### Credential Rotation
- HTTP auth re-resolved per-request to honor credential rotation
- SecretRef inspect/strict behavior aligned across preload and runtime paths
- Config snapshots redact `sourceConfig` and `runtimeConfig` alias fields

## SSRF Protection

### Browser SSRF
- Enforced on snapshot, screenshot, and tab routes
- Default hostname SSRF relaxed for loopback connections
- `cdp-reachability-policy` manages CDP readiness under strict defaults
- Local `attachOnly` loopback CDP sessions detected properly
- Managed loopback CDP startup and control unblocked under strict defaults
- Explicit strict SSRF config preserved

### MCP SSRF
- Loopback request validation hardened
- Media paths validated against local roots

### Media Path Security
- `localRoots` containment enforced on webchat audio embedding
- Remote-host `file://` URLs rejected in media embedding
- Media source URLs validated
- Host-local CSV/Markdown uploads allowed via Slack
- Fail-closed on attachment canonicalization failures

## Exec Approvals

### How It Works
1. Agent requests exec command
2. Gateway checks approval policy
3. If approval required, prompt sent to configured approvers
4. Approver approves/denies via button or CLI

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
        agentFilter: ["default"],      // optional agent allowlist
        sessionFilter: ["discord:"],   // optional session key patterns
        target: "dm",
        cleanupAfterResolve: false,
      },
    },
  },
}
```

In auto mode, exec approvals activate when approvers can be resolved.

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
- `configWrites: false` blocks platform-initiated config writes (Telegram, Slack)

## Config Safety
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
- Unknown Responses API failures classified for failover
- `connection-mismatch` replay errors classified as replay-invalid
- `No conversation found` classified as session_expired

### Resilience
- Inbound deduplication across restarts (Discord, BlueBubbles, WhatsApp)
- Reply queue splits batches by auth context
- Outbound delivery queue recovery for transient failures
- Tool loop detection prevents infinite retries
- Context engine graceful degradation on plugin resolution failure
