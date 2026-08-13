# OpenClaw Sessions

> Session management, keys, lifecycle, and configuration

## Session Types

| Type | Key Pattern | Use Case |
|------|-------------|----------|
| **Main** | `agent:<agentId>:main` | Default direct-chat session |
| **Per-peer DM** | `agent:<agentId>:direct:<peerId>` | Isolated per-sender DMs |
| **Per-channel DM** | `agent:<agentId>:<channel>:direct:<peerId>` | DM isolation by channel |
| **Group** | `agent:<agentId>:<channel>:group:<id>` | Group/channel chats |
| **Cron** | `cron:<jobId>` | Scheduled task sessions |
| **Webhook** | `hook:<uuid>` | Webhook-triggered sessions |

## DM Scoping (`session.dmScope`)

Controls how direct messages are grouped:

```json5
{
  session: {
    dmScope: "main",              // Default: all DMs share main session
    // dmScope: "per-peer",       // Isolate by sender across channels
    // dmScope: "per-channel-peer",     // Isolate by channel + sender
    // dmScope: "per-account-channel-peer", // Full isolation for multi-account
  },
}
```

**Security Warning**: For multi-user setups, use `per-channel-peer` or higher to prevent context leakage between users.

## Identity Links

Map the same person across channels to share their DM session:

```json5
{
  session: {
    dmScope: "per-peer",
    identityLinks: {
      alice: ["telegram:123456789", "discord:987654321012345678"],
      bob: ["whatsapp:+15555550123", "telegram:987654321"],
    },
  },
}
```

## Session Lifecycle

### Reset Policies

```json5
{
  session: {
    reset: {
      mode: "daily",      // Daily reset
      atHour: 4,          // At 4 AM local time
      idleMinutes: 120,   // Or after 2h idle (whichever first)
    },
    resetByType: {
      direct: { mode: "idle", idleMinutes: 240 },
      group: { mode: "idle", idleMinutes: 120 },
      thread: { mode: "daily", atHour: 4 },
    },
    resetByChannel: {
      discord: { mode: "idle", idleMinutes: 10080 }, // 7 days
    },
    resetTriggers: ["/new", "/reset"],
  },
}
```

### Manual Commands

| Command | Action |
|---------|--------|
| `/new` | Start fresh session |
| `/new <model>` | New session with specific model |
| `/reset` | Same as `/new` |
| `/status` | Show session info, context usage |
| `/compact` | Summarize old context |
| `/stop` | Abort current run |

## Storage

```
~/.openclaw/agents/<agentId>/sessions/
├── sessions.json              # Session store (key -> metadata)
├── <sessionId>.jsonl          # Transcript
├── <sessionId>-topic-<threadId>.jsonl  # Threaded sessions
└── *.deleted.<timestamp>      # Archived/deleted sessions
```

## Maintenance

```json5
{
  session: {
    maintenance: {
      mode: "enforce",        // "warn" | "enforce"
      pruneAfter: "30d",      // Remove stale sessions
      maxEntries: 500,        // Cap total entries
      rotateBytes: "10mb",    // Rotate store file
      maxDiskBytes: "1gb",    // Disk budget
      highWaterBytes: "800mb", // Cleanup threshold
    },
  },
}
```

CLI commands:
```bash
openclaw sessions cleanup --dry-run    # Preview
openclaw sessions cleanup --enforce    # Apply
openclaw sessions --json               # List all
```

## Send Policy

Block delivery for specific session types:

```json5
{
  session: {
    sendPolicy: {
      rules: [
        { action: "deny", match: { channel: "discord", chatType: "group" } },
        { action: "deny", match: { keyPrefix: "cron:" } },
      ],
      default: "allow",
    },
  },
}
```

Runtime overrides (owner only):
- `/send on` → allow
- `/send off` → deny
- `/send inherit` → use config rules

## Tips

1. **Single-user**: Default `dmScope: "main"` is fine
2. **Multi-user**: Use `per-channel-peer` minimum
3. **Multi-account**: Use `per-account-channel-peer`
4. **Cross-channel identity**: Configure `identityLinks`
5. **Production**: Enable `maintenance.mode: "enforce"`
