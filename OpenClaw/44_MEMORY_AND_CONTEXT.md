# Memory and Context

## Scope
MEMORY.md, daily memory files, context windows, and retrieval patterns. How agents manage persistent knowledge across sessions.

## Audience
- Operators configuring agent memory behavior
- LLM systems retrieving OpenClaw memory and context management patterns

## Memory Architecture

Agents wake up fresh each session. Files are their persistence mechanism.

```
~/.openclaw/workspace/
├── MEMORY.md              # Curated long-term memory (main session only)
├── SOUL.md                # Persona and behavior definition
├── USER.md                # Human preferences and context
├── AGENTS.md              # Workspace conventions and rules
├── HEARTBEAT.md           # Heartbeat task checklist
├── TOOLS.md               # Local tool notes
├── IDENTITY.md            # Agent identity
└── memory/
    ├── 2026-04-10.md      # Today's daily notes
    ├── 2026-04-09.md      # Yesterday's notes
    └── heartbeat-state.json  # Heartbeat check tracking
```

## File Roles

### MEMORY.md (Long-Term Memory)
- **Loaded in main sessions only** (direct chats with the human)
- **Not loaded in shared contexts** (Discord, group chats, other-user sessions)
- Curated, distilled knowledge — not raw logs
- Updated periodically from daily notes during heartbeats
- Contains: decisions, preferences, lessons learned, important context

### Daily Memory Files (`memory/YYYY-MM-DD.md`)
- Raw chronological notes of what happened each day
- Created automatically when agent writes observations
- Source material for MEMORY.md curation
- Retained for recent days; older files can be archived

### Workspace Context Files
- `SOUL.md`, `USER.md`, `AGENTS.md`, `IDENTITY.md` — Always loaded at session start
- `HEARTBEAT.md` — Loaded during heartbeat polls
- `TOOLS.md` — Environment-specific notes

## Memory Search

Agents use `memory_search` to semantically query memory files before answering questions about prior work, decisions, or preferences.

**Search scope:**
- `MEMORY.md`
- `memory/*.md`
- Optional: session transcripts (when configured)

**Usage pattern:**
```
User asks about prior work
    ↓
[memory_search triggered] → Retrieves relevant snippets
    ↓
[memory_get] → Pulls specific lines for verification
    ↓
Agent responds with source citations
```

## Context Window Management

Workspace files consume context tokens. Strategies to manage window budget:

| Strategy | Description | When to Use |
|----------|-------------|-------------|
| **Selective loading** | Only load relevant files per session type | Always (built-in) |
| **MEMORY.md gate** | Skip in shared/group contexts | Group chats, multi-user |
| **Daily file pruning** | Only load today + yesterday | Each session start |
| **Curation** | Distill daily notes into MEMORY.md | Periodic heartbeats |
| **File size limits** | Keep MEMORY.md concise | Ongoing maintenance |

## Memory Maintenance Workflow

Periodically (during heartbeats, every few days):

1. Read recent `memory/YYYY-MM-DD.md` files
2. Identify significant events, decisions, lessons
3. Update `MEMORY.md` with distilled knowledge
4. Remove outdated entries from `MEMORY.md`
5. Archive very old daily files if needed

## Security Considerations

- **MEMORY.md contains personal context** — never load in group/shared contexts
- **Daily notes may contain sensitive details** — same restriction applies
- **Agents are guests in the human's life** — treat all memory content with respect
- **No exfiltration** — memory files stay local, never sent externally without explicit request

## Related Documentation

- `44_MEMORY_AND_CONTEXT.md` — This document
- `40_AGENT_RUNTIME.md` — Agent execution and session lifecycle
- `70_CRON_AND_SCHEDULING.md` — Scheduled memory maintenance tasks
- `90_SECURITY_MODEL.md` — Security boundaries and data protection

## Provenance

- Memory system from `packages/agent/src/memory.ts`
- Context loading from `packages/agent/src/context.ts`
- Workspace conventions from OpenClaw default `AGENTS.md`
- Official docs: <https://docs.openclaw.ai>
- Repository: <https://github.com/openclaw/openclaw>
