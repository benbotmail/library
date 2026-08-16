# Session Model

## Scope
Session lifecycle: creation, isolation, persistence, sub-agents, and cleanup. How sessions work as the fundamental unit of conversation state in OpenClaw.

## Audience
- Operators managing session behavior and cleanup
- Developers building sub-agent orchestration patterns
- LLM systems retrieving OpenClaw session architecture context

## Session Lifecycle

```
[Trigger] → Create → Active → Idle → Timeout/Close
                ↑         ↓
                └── [New message reactivates]
```

**States:**
| State | Description |
|-------|-------------|
| **Active** | Agent is processing a turn (message + tool calls + response) |
| **Idle** | Turn complete, waiting for next message. Session history preserved. |
| **Timed out** | No activity within timeout window. Session reaped, history persisted to disk. |
| **Closed** | Explicitly ended via session management or Gateway restart. |

## Session Types

| Type | Creation Trigger | Lifetime | Isolation |
|------|-----------------|----------|-----------|
| **Main** | First DM or routed message from user | Persistent | Per-user per-channel |
| **Sub-agent (run)** | `sessions_spawn(mode="run")` | One-shot, auto-cleanup | Own workspace, inherits parent dir |
| **Sub-agent (session)** | `sessions_spawn(mode="session")` | Persistent until killed | Own workspace, inherits parent dir |
| **ACP harness** | `sessions_spawn(runtime="acp")` | Thread-bound or ephemeral | Isolated coding environment |
| **Cron-triggered** | Cron schedule fires | Ephemeral per invocation | Isolated context |

## Session Isolation

Each session maintains independent:

- **Message history** — Conversation turns unique to that session
- **Tool call results** — Tool outputs scoped to session context
- **Workspace access** — Sub-agents inherit workspace directory but have own execution context
- **Model configuration** — Per-session model overrides independent of other sessions

**Shared across sessions:**
- Workspace files (MEMORY.md, SOUL.md, etc.)
- Configuration (agents, tools, channels)
- Plugin state

## Persistence

Sessions persist conversation history to disk:

- **Storage location:** `~/.openclaw/sessions/` (default)
- **Format:** JSONL or structured storage
- **Retention:** Configurable; old sessions auto-cleaned
- **Recovery:** Sessions resume after Gateway hot reload; full restart creates fresh sessions

## Sub-Agent Orchestration

### Spawning
```
Main session
    ↓ sessions_spawn(task="...", mode="run")
Sub-agent created
    ↓ Executes task
    ↓ Auto-announces completion
Result pushed to parent session
```

### Management
| Action | Tool | Purpose |
|--------|------|---------|
| List sub-agents | `subagents(action="list")` | Check running sub-agents |
| Send guidance | `subagents(action="steer")` | Inject message into running sub-agent |
| Terminate | `subagents(action="kill")` | Force-stop a sub-agent |
| Check history | `sessions_history(sessionKey)` | Retrieve sub-agent conversation |

### ACP Harness Sessions
For coding agents (Codex, Claude Code, Pi):
- Use `runtime="acp"` with required `agentId`
- Thread-bound sessions (`thread: true`) for Discord persistent coding flows
- Do not route through `subagents list` — use `sessions_list` instead
- Inherit workspace automatically

## Session Context Loading

At session start, the Gateway loads context files:

| File | When Loaded | Scope |
|------|------------|-------|
| `AGENTS.md` | Every session | Workspace rules and conventions |
| `SOUL.md` | Every session | Agent persona and tone |
| `USER.md` | Every session | Human context and preferences |
| `TOOLS.md` | Every session | Local tool configuration notes |
| `HEARTBEAT.md` | Heartbeat sessions only | Periodic task checklist |
| `MEMORY.md` | Main sessions only | Long-term memory (security: not loaded in shared contexts) |
| `memory/YYYY-MM-DD.md` | Recent days | Daily context |

**Security note:** MEMORY.md is only loaded in main sessions (direct DMs with the human) to prevent leaking personal context in group chats or shared sessions.

## Cleanup and Timeouts

| Mechanism | Default | Configurable |
|-----------|---------|-------------|
| Session idle timeout | ~30 min | Yes |
| Sub-agent run timeout | Per `runTimeoutSeconds` | Yes |
| Orphan cleanup | On Gateway start | Automatic |
| History retention | 30 days | Configurable |

**Recent-session protection (new):** `sessions.maintenance.preserveRecent` (e.g. `"7d"`; disabled when omitted or `false`) protects recently active interactive sessions and their SQLite history generations from entry/disk-budget cleanup. Default installs keep the oldest-first policy; synthetic model-run/cron/hook/heartbeat/ACP/sub-agent sessions stay eligible. Protection can temporarily hold the store above targets and expires after the inactivity window. Archiving and pinning remain explicit user actions exempt from all automatic maintenance.

## Related Documentation

- `20_GATEWAY_COMPONENTS.md` — Overall Gateway architecture
- `22_ROUTING_AND_DISPATCH.md` — How messages are routed to sessions
- `40_AGENT_RUNTIME.md` — Agent execution within sessions
- `44_MEMORY_AND_CONTEXT.md` — Memory files and context window management
- `70_CRON_AND_SCHEDULING.md` — Cron-triggered session patterns

## Provenance

- Session management from `packages/sessions/`
- Sub-agent orchestration from `packages/subagents/`
- Official docs: <https://docs.openclaw.ai>
- Repository: <https://github.com/openclaw/openclaw>
