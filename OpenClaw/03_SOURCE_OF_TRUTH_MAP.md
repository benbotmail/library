# Source of Truth Map

When multiple sources disagree, use this authority order to resolve conflicts.

## Authority hierarchy (highest to lowest)

### 1. Source code (`src/`)
The running Gateway implementation is the ultimate source of truth.

**When to check:**
- Behavior differs from documentation
- API signature unclear
- Edge cases not documented

**Key directories:**
- `src/gateway/` — Gateway entry points and daemon logic
- `src/channels/` — Channel implementations
- `src/agents/` — Agent runtime and sessions
- `src/plugins/` — Plugin system
- `src/routing/` — Message routing logic
- `src/context-engine/` — Context injection
- `src/config/` — Config schema and validation

**Example:** To confirm how `dmPolicy` works, read `src/channels/*/channel.ts` implementation.

### 2. Config schema (`src/config/`)
The config schema defines all valid configuration keys and their types.

**When to check:**
- Validating a config field name
- Checking allowed values for an enum
- Understanding nested structure

**How to access:**
- Run `openclaw config schema` to dump full schema
- Run `openclaw config schema.lookup <path>` for specific fields

**Example:** `openclaw config.schema.lookup agents.defaults.model.primary`

### 3. Official docs (`docs/`)
The canonical documentation at <https://docs.openclaw.ai>.

**When to check:**
- Understanding high-level concepts
- Learning workflows and patterns
- Troubleshooting guided by docs

**Priority within docs:**
- Concept docs (`docs/concepts/`) — Higher authority than reference
- Reference docs (`docs/reference/`) — Lower authority than concepts
- Guides (`docs/start/`, `docs/gateway/`) — Mixed authority

### 4. README.md and AGENTS.md
Project-level documentation in the repository root.

**When to check:**
- Quick start instructions
- Project overview
- Agent behavior guidance (AGENTS.md)

### 5. Skill SKILL.md files
Skill-specific conventions and workflows.

**When to check:**
- Using a specific skill (e.g., `coding-agent`, `github`)
- Understanding skill triggers and conditional logic
- Following skill-specific tool conventions

**Location:** `skills/*/SKILL.md` or `~/.npm-global/lib/node_modules/openclaw/skills/*`

### 6. CHANGELOG.md
Historical record of changes and migrations.

**When to check:**
- Understanding when a feature was added
- Checking breaking changes in migrations
- Verifying current version behavior

### 7. Community resources
Discord, GitHub discussions, and third-party guides.

**Authority level:** Lowest. Use only for inspiration; verify against higher sources.

## Conflict resolution examples

### Example 1: Config field name
**Question:** Is it `channels.whatsapp.enabled` or `channels.whatsapp.enable`?

**Resolution:**
1. Check config schema: `openclaw config.schema.lookup channels.whatsapp`
2. If schema missing, check source: `src/channels/whatsapp/channel.ts`
3. Docs are secondary; may have typos

### Example 2: dmPolicy behavior
**Question:** Does `"pairing"` require manual approval every time?

**Resolution:**
1. Check source code for `dmPolicy` handling in channel implementation
2. Check `docs/channels/whatsapp.md` for documentation
3. If they differ, source code wins (may be a doc bug to report)

### Example 3: Agent tool permissions
**Question:** Can sub-agents access all tools by default?

**Resolution:**
1. Check source: `src/agents/subagent.ts` for tool permission inheritance
2. Check AGENTS.md for documented behavior
3. Check `docs/concepts/multi-agent.md` for guidance

### Example 4: Cron schedule syntax
**Question:** Does `everyMs` accept string values like `"5m"`?

**Resolution:**
1. Check config schema for `cron.job.schedule.everyMs` type
2. Check source: `src/cron/scheduler.ts` for parsing logic
3. Docs may use human-friendly examples but schema is authoritative

## When to trust each source

### Trust source code when:
- API behavior differs from docs
- Edge cases are unclear
- Implementation details matter
- Documentation is ambiguous

### Trust config schema when:
- Validating field names and paths
- Checking data types and allowed values
- Understanding nested structures
- Schema errors occur

### Trust docs when:
- Learning concepts and workflows
- Following guided tutorials
- Understanding high-level design
- Troubleshooting known issues

### Trust CHANGELOG when:
- Checking when features were added
- Understanding migration paths
- Verifying version-specific behavior

### Trust community when:
- Getting inspiration for workflows
- Finding workarounds for edge cases
- Learning from others' experiences

## Common conflict patterns

### Pattern 1: Outdated docs
**Symptom:** Docs describe a feature that no longer works

**Resolution:**
1. Check source code for current behavior
2. Check CHANGELOG for removal/deprecation
3. Consider submitting a PR to update docs

### Pattern 2: Typo in docs
**Symptom:** Docs show `channels.whatsapp.enabled` but schema expects `enabled`

**Resolution:**
1. Check config schema for correct field name
2. Trust schema and source code
3. Report doc bug if confirmed

### Pattern 3: Ambiguous terminology
**Symptom:** "session" used inconsistently across docs

**Resolution:**
1. Check source code for precise definition
2. Check `docs/concepts/` for canonical terminology
3. Use `02_CONCEPTS_AND_TERMINOLOGY.md` as reference

### Pattern 4: Version mismatch
**Symptom:** Docs describe `main` branch behavior but you're on `stable`

**Resolution:**
1. Check your version: `openclaw --version`
2. Check CHANGELOG for version-specific behavior
3. Consider upgrading if feature is important

## Verification workflow

When you need definitive answers:

1. **Define the question** — Be specific (field name, behavior, edge case)
2. **Check schema** — `openclaw config.schema.lookup <path>`
3. **Check source** — Read relevant `src/` files
4. **Check docs** — Verify against official documentation
5. **Check CHANGELOG** — Confirm version-specific behavior
6. **Document findings** — Update docs if conflicts exist

## Reporting conflicts

If you find inconsistencies between sources:

1. Verify which source is correct (usually source code)
2. Check GitHub for existing issues
3. Open an issue with:
   - The conflicting information
   - Your investigation (schema, source, docs)
   - Suggested resolution
4. Consider submitting a PR to fix docs or schema

## Provenance
- **Source:** README.md, docs/gateway/configuration.md, AGENTS.md
- **Last validated:** 2026-03-18 (against openclaw@latest from GitHub)
