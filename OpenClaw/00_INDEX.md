# OpenClaw LLM Documentation Pack

> **Current state:** OpenClaw v2026.8.1 · upstream `cd7b7f6` · last refreshed 2026-08-10

This collection is a practical, retrieval-friendly guide to understanding, configuring, and troubleshooting OpenClaw — a self-hosted multi-channel AI assistant gateway.

## Quick reference files

These files are concise, current-state reference docs kept in sync with upstream:

- `OVERVIEW.md` — High-level architecture, core concepts, CLI quick reference
- `CONFIG.md` — Config file locations, precedence, key sections
- `CHANNELS.md` — All supported channels with config, streaming modes, DM/group policy
- `SESSIONS.md` — Session model, heartbeat config (including `directPolicy`), cron scheduling
- `TOOLS.md` — Built-in tools reference
- `QUICKREF.md` — Fast command lookup
- `TROUBLESHOOTING.md` — Common issues and fixes
- `README.md` — Pack description

## Full documentation library

### Core orientation
- `00_INDEX.md` — This file
- `01_ARCHITECTURE_OVERVIEW.md` — System architecture, components, and data flows
- `02_CONCEPTS_AND_TERMINOLOGY.md` — Core concepts and their relationships
- `03_SOURCE_OF_TRUTH_MAP.md` — Authority order for OpenClaw sources
- `04_DOCUMENTATION_CONVENTIONS.md` — Documentation rules and provenance

### Quick start
- `10_GETTING_STARTED_ROUTE.md` — End-to-end setup
- `11_ONBOARDING_WORKFLOWS.md` — Onboarding paths (CLI wizard, Control UI, manual)

### Gateway core
- `20_GATEWAY_COMPONENTS.md` — Gateway architecture internals
- `21_SESSION_MODEL.md` — Session lifecycle and persistence
- `23_CONFIGURATION_SCHEMA_REFERENCE.md` — **Canonical config schema reference** (v2026.8.1)
- `24_HOT_RELOAD_AND_RESTART.md` — Config hot-reload and daemon restarts

### Channels and messaging
- `CHANNELS.md` — **Canonical channels reference** (v2026.8.1: Telegram streaming modes, all channels)
- `32_DM_AND_GROUP_POLICIES.md` — DM and group policy details

### Agents and tools
- `41_MODEL_CONFIGURATION.md` — Model selection, aliases, fallbacks
- `44_MEMORY_AND_CONTEXT.md` — Memory files and context windows
- `51_BUILT_IN_TOOLS_REFERENCE.md` — Core tools reference
- `70_CRON_AND_SCHEDULING.md` — Cron jobs reference
- `90_SECURITY_MODEL.md` — Security, approvals, and access control

### Reference
- `130_COMMAND_LINE_REFERENCE.md` — CLI commands
- `132_ENVIRONMENT_VARIABLES.md` — Environment variables

## External references
- Upstream repo: <https://github.com/openclaw/openclaw>
- Official docs: <https://docs.openclaw.ai>
- Community Discord: <https://discord.gg/clawd>

---

## Provenance
- **Upstream commit:** `cd7b7f639da0d26424b52f3ffa2391f81acb5040`
- **OpenClaw version:** `2026.8.1`
- **Last refresh:** 2026-08-10
