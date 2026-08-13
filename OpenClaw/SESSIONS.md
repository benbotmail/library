# OpenClaw Sessions Reference

> Current as of 2026-08-13 (upstream `0926d56cbf9`).

## Session Model

A **session** is an independent conversation context with its own message history. OpenClaw routes inbound messages to sessions based on channel, sender, and scope.

## Session Types

| Type | Key Pattern | Description |
|------|-------------|-------------|
| Main | `agent:<agentId>:main` | Direct chat with the agent (primary context) |
| Group | `agent:<agentId>:<channel>:<groupId>` | Per-group conversation |
| DM | `agent:<agentId>:<channel>:<userId>` | Per-user DM conversation |
| Topic | `agent:<agentId>:<channel>:<groupId>:topic:<threadId>` | Forum topic / thread |
| Isolated | `agent:<agentId>:automation:<jobId>` | Fresh session for automation/cron jobs |
| Sub-agent | `agent:<agentId>:subagent:<uuid>` | Child session spawned by another agent |

## DM Scope

```json5
{
  session: {
    dmScope: "per-channel-peer",  // Recommended for multi-user
    // Options:
    // "main"                  — shared main session (single user)
    // "per-peer"              — per-user sessions across all channels
    // "per-channel-peer"      — per-user per-channel sessions
    // "per-account-channel-peer" — per-user per-account per-channel
  },
}
```

## Session Reset

```json5
{
  session: {
    reset: {
      mode: "daily",       // daily | manual | idle
      atHour: 4,           // Hour for daily reset (0-23)
      idleMinutes: 120,    // Idle timeout for idle mode
    },
  },
}
```

## Thread Bindings

Discord threads, Telegram topics, and other thread-like surfaces can bind sessions:

```json5
{
  session: {
    threadBindings: {
      enabled: true,
      idleHours: 24,
      maxAgeHours: 0,    // 0 = no max age
    },
  },
}
```

Commands: `/focus`, `/unfocus`, `/agents`, `/session idle`, `/session max-age`.

## Session Maintenance

```json5
{
  session: {
    maintenance: {
      mode: "enforce",     // enforce | advisory
      pruneAfter: "30d",
      maxEntries: 500,
    },
  },
}
```

## Sub-Agent Sessions

Sub-agent sessions are spawned by the parent agent for isolated work:

- **`runtime: "subagent"`**: OpenClaw native sub-agent
- **`runtime: "acp"`**: ACP harness (Codex, Claude Code, Pi)
- **`mode: "run"`**: One-shot task
- **`mode: "session"`**: Persistent/thread-bound session
- **`thread: true`**: Discord thread-bound (ACP harness)

Sub-agents inherit the parent workspace directory automatically.

## Session Visibility

Session visibility determines which sessions an agent can see and interact with. Protected sessions are excluded from entry caps.

## Context and Compaction

- Each session maintains conversation history with bounded context
- Compaction summarizes older history when context grows too large
- `session.highWaterBytes` controls when pruning triggers (0 is valid, does not delete all history)
- Compaction preserves structure history for audit

## Session Search

Sessions can be searched via `sessions_list` with filters for kind, recent activity, and last messages.

## CLI

```bash
openclaw sessions               # List sessions
openclaw sessions --json        # JSON output
openclaw transcripts            # View transcripts
```
