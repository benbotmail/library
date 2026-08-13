# OpenClaw LLM Documentation Pack

This collection is a practical, retrieval-friendly guide to understanding, configuring, and troubleshooting OpenClaw — a self-hosted multi-channel AI assistant gateway.

## Core orientation
- `01_ARCHITECTURE_OVERVIEW.md` — System architecture, components, and data flows.
- `02_CONCEPTS_AND_TERMINOLOGY.md` — Core concepts (Gateway, agents, sessions, nodes, plugins) and their relationships.
- `03_SOURCE_OF_TRUTH_MAP.md` — Authority order for OpenClaw sources when claims conflict.
- `04_DOCUMENTATION_CONVENTIONS.md` — Documentation rules for structure, claim grounding, and provenance.

## Quick start and onboarding
- `10_GETTING_STARTED_ROUTE.md` — End-to-end setup from first install to messaging an agent.

## Gateway core
- `20_GATEWAY_COMPONENTS.md` — Gateway architecture: entry points, routing engine, session management, plugins.
- `21_SESSION_MODEL.md` — Session lifecycle: creation, isolation, persistence, sub-agents, and cleanup.
- `23_CONFIGURATION_SCHEMA_REFERENCE.md` — Core config schema sections with examples for common patterns.
- `24_HOT_RELOAD_AND_RESTART.md` — Config hot-reload (hybrid mode), daemon restarts, and state preservation.

## Channels and messaging
- `CHANNELS.md` — All supported channels, routing, DM/group policies, streaming modes.
- `32_DM_AND_GROUP_POLICIES.md` — `dmPolicy` and `groupPolicy`: allowlist, pairing, open, mention patterns.

## Agents and LLM integration
- `41_MODEL_CONFIGURATION.md` — Model selection, aliases, fallbacks, failover, and custom providers.
- `44_MEMORY_AND_CONTEXT.md` — MEMORY.md, daily memory files, context windows, and retrieval patterns.

## Tools and capabilities
- `TOOLS.md` — Tool execution model: permissions, approvals, sandboxing, and tool availability.
- `51_BUILT_IN_TOOLS_REFERENCE.md` — Core tools: exec, browser, web_search, message, sessions_spawn, cron, etc.

## Automation
- `70_CRON_AND_SCHEDULING.md` — Cron jobs, heartbeat, scheduling semantics.

## Security and operations
- `81_SSH_AND_REMOTE_SETUP.md` — SSH configuration, remote access, and node pairing.
- `90_SECURITY_MODEL.md` — Sandbox, tool policy, elevated exec, and security layers.
- `110_TROUBLESHOOTING_ENTRY_POINTS.md` — Common issues and diagnostic flows.
- `130_COMMAND_LINE_REFERENCE.md` — CLI commands reference.
- `132_ENVIRONMENT_VARIABLES.md` — Environment variables reference.
- `133_PROVENANCE_COVERAGE.md` — Provenance tracking and coverage.

## Quick reference sheets
- `OVERVIEW.md` — High-level overview and architecture diagram.
- `CONFIG.md` — Configuration quick reference.
- `QUICKREF.md` — Fast lookup for common tasks.
- `SESSIONS.md` — Session model quick reference.
- `TROUBLESHOOTING.md` — Troubleshooting quick reference.
