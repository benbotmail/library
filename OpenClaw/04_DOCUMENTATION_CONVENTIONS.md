# Documentation Conventions

> Rules for structure, claim grounding, and provenance

## Structure Principles

- **Current-state canonical**: Document how things work NOW, not how they changed over time. Avoid changelog-style phrasing ("previously...", "as of v2.x...").
- **Actionable**: Every section should help the reader do something or understand something they need to act.
- **Hierarchical headings**: Use clear H2/H3 structure for scanability.
- **Code examples**: Always show JSON5 config blocks with realistic values.
- **Cross-references**: Link related sections within the doc pack rather than repeating content.

## Configuration Documentation Rules

- Show the **current accepted keys** with their types, defaults, and valid values.
- Distinguish between root-level config (infrastructure) and `agents.defaults` (agent-loop behavior).
- Note when per-agent overrides (`agents.entries.*`) **replace** rather than merge with defaults.
- Always mention `openclaw doctor` when covering keys it can validate or fix.

## Provenance

- Each file in this pack maps to specific upstream source docs.
- When a claim is surprising or nuanced, note which source doc it comes from.
- The `03_SOURCE_OF_TRUTH_MAP.md` file defines the authority hierarchy.

## Terminology

- Use the terms defined in `02_CONCEPTS_AND_TERMINOLOGY.md` consistently.
- Capitalize proper nouns: Gateway, Workspace, Main Session.
- Use code formatting for config keys (`agents.defaults.model`), CLI commands (`openclaw gateway`), and identifiers.

## What to Include

- Config keys with types, defaults, and valid values
- CLI commands with common flags
- Behavioral semantics (what actually happens)
- Common patterns and examples
- Troubleshooting entry points

## What to Exclude

- Internal implementation details unless operationally relevant
- Historical/changelog information
- Speculative or future-planned features
- Redundant restatements of the same fact across files
