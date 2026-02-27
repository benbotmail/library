# 07 — Agent Workspace, Memory, Skills

Primary refs:
- `openclaw-src/docs/tools/skills.md`
- `openclaw-src/docs/tools/skills-config.md`

## Workspace model
Common workspace root: `~/.openclaw/workspace`.
Behavior/persona files (for example `AGENTS.md`, `SOUL.md`, `USER.md`) shape assistant behavior in local deployments.

## Memory model
- Session transcripts are ephemeral context.
- Durable memory should be file-backed (for example `MEMORY.md`, `memory/*.md`).
- If continuity matters, write it to disk.

## Skills model
- Skills are focused instruction packs (`SKILL.md`) for repeatable workflows.
- Select the most specific matching skill before task execution.
- Keep external-action skills explicit and safety-bounded.
