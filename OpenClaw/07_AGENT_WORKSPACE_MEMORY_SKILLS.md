# 07 — Agent Workspace, Memory, Skills

## Agent workspace model
Typical workspace root: `~/.openclaw/workspace` (configurable). Prompt/context files are injected from workspace conventions (`AGENTS.md`, `SOUL.md`, `TOOLS.md`, etc. depending setup).

## Memory model (conceptual)
- Session history + explicit memory files can coexist.
- Memory tooling includes indexing/search capabilities.
- Keep sensitive data discipline; do not over-share across contexts.

## Skills model
- Skills are structured capability bundles with prerequisites and docs.
- Skills can be bundled/managed/workspace-scoped.
- Skill docs are first stop for specialized workflows.

## Bot authoring recommendations
- Document any new skill with: purpose, when-to-use, constraints, examples.
- Keep procedures deterministic and auditable.
- Add safety notes for external side effects.

## Useful references
- `openclaw-src/docs/concepts/memory.md`
- `openclaw-src/docs/tools/skills.md`
- `openclaw-src/docs/tools/skills-config.md`
- `openclaw-src/docs/concepts/session*.md`
