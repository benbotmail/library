# 07 — Agent Workspace, Memory, Skills

## Workspace model
Default workspace root is typically `~/.openclaw/workspace`.
Bootstrap/persona files (for example `AGENTS.md`, `SOUL.md`, `USER.md`) shape behavior.

## Memory model
- Short-term: session transcripts.
- Durable notes: workspace memory files (`MEMORY.md`, `memory/*.md`).
- Use explicit file writes for persistence across restarts.

## Skills model
- Skills are scoped instruction packs (`SKILL.md`) for specific workflows.
- Choose the most specific matching skill before execution.
- Keep skill behavior deterministic and safety-aware for external actions.

References:
- `openclaw-src/docs/tools/skills.md`
- `openclaw-src/docs/tools/skills-config.md`
