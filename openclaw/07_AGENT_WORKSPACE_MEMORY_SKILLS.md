---
summary: "Agent workspace, memory system, active memory, dreaming, skills, and experimental features"
read_when:
  - Setting up memory or active memory
  - Understanding workspace bootstrap files
  - Configuring dreaming or skills
title: "Agent Workspace, Memory, and Skills"
---

# Agent Workspace, Memory, and Skills

## Workspace

The workspace is a directory (default: `~/.openclaw/workspace`) containing:
- **SOUL.md** — Agent personality and tone
- **USER.md** — Information about the human
- **AGENTS.md** — Session behavior rules
- **MEMORY.md** — Curated long-term memory (loaded in main session only)
- **memory/*.md** — Daily notes and raw memory files
- **HEARTBEAT.md** — Heartbeat checklist (injected during heartbeat runs)
- **TOOLS.md** — Local tool configuration notes

Bootstrap files are injected into the system prompt. `lightContext` mode keeps only `HEARTBEAT.md`.

## Memory System

### Memory Search
Semantic search over `MEMORY.md` and `memory/*.md` using embeddings.

Configuration under `agents.defaults.memorySearch`:
```json5
{
  agents: {
    defaults: {
      memorySearch: {
        provider: "openai",     // openai | gemini | ollama | (auto-detected)
        model: "text-embedding-3-small",
        fallback: "gemini",     // optional provider failover
      },
    },
  },
}
```

Embedding providers:
- **OpenAI**: `text-embedding-3-small` (recommended)
- **Gemini**: `gemini-embedding-001`
- **Ollama**: `nomic-embed-text` (local)
- **GitHub Copilot**: New embedding provider for memory search
- **LanceDB**: Cloud storage support for memory-lancedb

Session memory search (experimental): `agents.defaults.memorySearch.experimental.sessionMemory`

### Active Memory
Plugin-owned blocking sub-agent that runs before each main reply. Injects relevant memories as a **hidden prompt prefix** (not user-visible).

```json5
{
  plugins: {
    entries: {
      "active-memory": {
        enabled: true,
        config: {
          agents: ["main"],
          allowedChatTypes: ["direct"],
          queryMode: "recent",      // message | recent | full
          promptStyle: "balanced",  // balanced | strict | contextual | recall-heavy | precision-heavy | preference-only
          timeoutMs: 15000,
          maxSummaryChars: 220,
          modelFallback: "google/gemini-3-flash",
        },
      },
    },
  },
}
```

Key points:
- Runs only on eligible interactive persistent chat sessions
- Model resolution: explicit → session model → agent model → fallback
- `persistTranscripts: true` keeps blocking sub-agent transcripts for debugging
- Speed tip: use a dedicated fast model like `cerebras/gpt-oss-120b` for lower latency
- Session toggle: `/active-memory on|off|status`

### Dreaming
Automated memory consolidation via the `memory-core` plugin.

```json5
{
  plugins: {
    entries: {
      "memory-core": {
        config: {
          dreaming: {
            enabled: true,
            storage: {
              mode: "separate",  // DEFAULT: phase blocks don't pollute daily files
            },
          },
        },
      },
    },
  },
}
```

Key changes:
- `storage.mode` defaults to `"separate"` — dreaming phase blocks no longer pollute daily memory files
- Dreaming uses ingestion date (not file date) for dayBucket
- Self-ingestion is blocked (dreaming won't ingest its own output)
- Runs once per cron schedule
- Transcript ingestion skipped via session store

## Skills

Skills provide specialized instructions for specific tasks. Loaded on demand from `~/.openclaw/skills/` or bundled skills.

Agent filter controls which agents can access which skills. Skills are injected into the system prompt when relevant.

### Skill Types
- **Bundled**: Ship with OpenClaw (weather, github, coding-agent, etc.)
- **Custom**: Created by users or agents

## Agent Configuration

### Model Selection
```json5
{
  agents: {
    defaults: {
      model: {
        primary: "openai/gpt-4.1",
        fallback: "openai/gpt-4.1-mini",
      },
    },
  },
}
```

Model selection resolution chain: explicit override → agent primary → fallback.

### Thinking Levels
`off` | `minimal` | `low` | `medium` | `high` | `xhigh` | `adaptive`

Default thinking varies by model. Per-model thinking defaults are maintained.

### Agent Scope
Agent scope config controls which agents have access to what:
- Tool access
- Skill filtering
- Channel binding
- Model restrictions

### Context Window
- Context-window guard tightens limits for small models
- Memory excerpts are bounded
- Compaction reserve floor capped to model's context window

## Experimental Features

Opt-in preview surfaces behind explicit flags. Shape may change.

```json5
{
  agents: {
    defaults: {
      experimental: {
        localModelLean: true,  // trim heavyweight tools for small local models
      },
    },
  },
  tools: {
    experimental: {
      planTool: true,  // structured update_plan for multi-step work
    },
  },
}
```

### Local Model Lean Mode
Trims heavyweight default tools (browser, cron, message) so the prompt is smaller for small-context or stricter backends. Not the normal path — leave off if your backend handles the full runtime.

## QMD Manager
Handles `.qmd` (quantized markdown) memory files:
- Slugified paths for consistent file naming
- Session files support
- Canonical memory path alignment
- Short-term memory promotion
