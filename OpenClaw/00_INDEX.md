# OpenClaw LLM Documentation Pack

This collection is a practical, retrieval-friendly guide to understanding, configuring, and troubleshooting OpenClaw — a self-hosted multi-channel AI assistant gateway.

## Core orientation
- `01_ARCHITECTURE_OVERVIEW.md` — System architecture, components, and data flows.
- `02_CONCEPTS_AND_TERMINOLOGY.md` — Core concepts (Gateway, agents, sessions, nodes, plugins) and their relationships.
- `03_SOURCE_OF_TRUTH_MAP.md` — Authority order for OpenClaw sources (docs, code, schema, community) when claims conflict.
- `04_DOCUMENTATION_CONVENTIONS.md` — Documentation rules for structure, claim grounding, and provenance.
- `05_CONFIGURATION_LANDSCAPE.md` — Configuration file locations, precedence, and hot-reload behavior.

## Quick start and onboarding
- `10_GETTING_STARTED_ROUTE.md` — End-to-end setup from first install to messaging an agent.
- `11_ONBOARDING_WORKFLOWS.md` — Onboarding paths: CLI wizard vs. Control UI vs. manual config.
- `12_PLATFORM_SPECIFIC_SETUP.md` — Setup nuances for macOS, Linux, Windows (WSL2), Docker, and Nix.

## Gateway core
- `20_GATEWAY_COMPONENTS.md` — Gateway architecture: entry points, routing engine, session management, plugins.
- `21_SESSION_MODEL.md` — Session lifecycle: creation, isolation, persistence, sub-agents, and cleanup.
- `22_ROUTING_AND_DISPATCH.md` — Request routing: channel → agent → tools, with multi-agent patterns.
- `23_CONFIGURATION_SCHEMA_REFERENCE.md` — Core config schema sections with examples for common patterns.
- `24_HOT_RELOAD_AND_RESTART.md` — Config hot-reload, daemon restarts, and state preservation rules.

## Channels and messaging
- `30_CHANNEL_OVERVIEW.md` — All supported channels (WhatsApp, Telegram, Discord, etc.) and their patterns.
- `31_CHANNEL_SETUP_CHECKLISTS.md` — Per-channel setup: authentication, permissions, webhooks, pairing.
- `32_DM_AND_GROUP_POLICIES.md` — `dmPolicy` and `groupPolicy`: allowlist, pairing, open, mention patterns.
- `33_MESSAGE_FORMATS_AND_RENDERING.md` — Text, media, reactions, mentions, buttons, and platform quirks.
- `34_MEDIAPHANDLING.md` — Image/audio/document upload/download, transcoding, and limits.

## Agents and LLM integration
- `40_AGENT_RUNTIME.md` — Agent execution: main sessions, sub-agents, ACP harnesses, and agent IDs.
- `41_MODEL_CONFIGURATION.md` — Model selection, aliases, fallbacks, failover, and custom providers.
- `42_THINKING_AND_REASONING_MODES.md` — Thinking levels, reasoning toggles, and model overrides.
- `43_MULTI_AGENT_ROUTING.md` — Per-agent sessions, workspace isolation, routing rules, and escalation.
- `44_MEMORY_AND_CONTEXT.md` — MEMORY.md, daily memory files, context windows, and retrieval patterns.

## Tools and capabilities
- `50_TOOL_SYSTEM_OVERVIEW.md` — Tool execution model: permissions, approvals, sandboxing, and tool availability.
- `51_BUILT_IN_TOOLS_REFERENCE.md` — Core tools: exec, browser, web_search, message, sessions_spawn, cron, etc.
- `52_EXEC_SECURITY_AND_APPROVALS.md` — Elevated commands, approval flows, dangerous patterns, and best practices.
- `53_BROWSER_CONTROL.md` — Browser automation: openclaw, user-browser, Chrome extension, and remote targets.
- `54_NODE_PAIRING_AND_CANVAS.md` — Mobile nodes, pairing, Canvas rendering, camera, and device actions.

## Plugins and extensions
- `60_PLUGIN_ARCHITECTURE.md` — Plugin system: entry points, config schemas, providers, and channels.
- `61_CORE_PLUGINS_REFERENCE.md` — Built-in plugins: brave-search, github, weather, gh-issues, coding-agent, etc.
- `62_CUSTOM_PLUGIN_DEVELOPMENT.md` — Writing plugins: SDK, manifest, tools, channels, providers.
- `63_SKILLS_SYSTEM.md` — Skills: discovery, SKILL.md conventions, conditional logic, and skill-creator workflow.

## Automation and workflows
- `70_CRON_AND_SCHEDULING.md` — Cron jobs: schedules, payloads, delivery modes, wake events, and reminders.
- `71_HOOKS_AND_AUTOMATION.md` — Hooks: before/after agent turns, system events, and custom workflows.
- `72_WEBHOOK_AND_EVENT_DELIVERY.md` — Webhook payloads, event types, signatures, and integration patterns.
- `73_AUTO_REPLY_PATTERNS.md` — Auto-reply rules: triggers, templates, channel filters, and limits.

## Networking and remote access
- `80_LOCALHOST_AND_PORTS.md` — Default ports, port binding, and firewall considerations.
- `81_SSH_AND_REMOTE_SETUP.md` — Remote Gateway access via SSH tunneling, systemd, and systemd user services.
- `82_TAILSCALE_AND_VPN_ACCESS.md` — Tailscale integration, node discovery, and tailnet routing.
- `83_REVERSE_TUNNELS_AND_RELAYS.md` — Cloudflare tunnel, ngrok, and NAT traversal patterns.

## Security and permissions
- `90_SECURITY_MODEL.md` — Token storage, allowlists, pairing codes, and isolation boundaries.
- `91_PERMISSIONS_AND_SANDBOXING.md` — Filesystem access, network boundaries, container isolation, and host access.
- `92_SECRET_MANAGEMENT.md` — Environment variables, secret injection, secret rotation, and .secrets.baseline.
- `93_AUDIT_AND_LOGGING.md` — Audit trails, logging levels, log aggregation, and retention policies.

## Development and debugging
- `100_DEVELOPMENT_SETUP.md` — Local dev: repo clone, pnpm setup, hot-reload, and test patterns.
- `101_TESTING_STRATEGIES.md` — Unit tests, e2e tests, channel fixtures, and integration testing.
- `102_DEBUGGING_TECHNIQUES.md` — Debug mode, verbose logging, breakpoints, and common failure modes.
- `103_DIAGNOSTICS_AND_HEALTH.md` — `openclaw doctor`, `openclaw health`, and diagnostic data collection.

## Troubleshooting and escalation
- `110_TROUBLESHOOTING_ENTRY_POINTS.md` — Symptom-based routing: installation, channels, agents, tools, networking.
- `111_COMMON_ERROR_PATTERNS.md` — Error messages and their causes: config validation, auth failures, rate limits, etc.
- `112_LOG_ANALYSIS_GUIDE.md` — Interpreting Gateway logs, agent logs, and channel logs.
- `113_ESCALATION_HANDOFF.md` — When and how to escalate: bug reports, feature requests, community support.

## Migration and upgrade paths
- `120_VERSIONING_AND_CHANNELS.md` — Stable, beta, dev channels, release tags, and upgrade strategies.
- `121_MIGRATION_GUIDES.md` — Config migrations, channel migrations, and breaking changes.
- `122_BACKUP_AND_RESTORE.md` — Config backup, workspace backup, and disaster recovery.

## Reference materials
- `130_COMMAND_LINE_REFERENCE.md` — All CLI commands: gateway, agent, channels, config, doctor, etc.
- `131_CONFIG_SCHEMA_INDEX.md` — Dot-notation index for all config paths with types and defaults.
- `132_ENVIRONMENT_VARIABLES.md` — Environment variable overrides and their config equivalents.
- `133_PROVENANCE_COVERAGE.md` — Audit report confirming source references across docs pages.

## External references
- Upstream repo: <https://github.com/openclaw/openclaw>
- Official docs: <https://docs.openclaw.ai>
- Community Discord: <https://discord.gg/clawd>
