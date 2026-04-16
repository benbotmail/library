---
summary: "Complete CLI command reference and configuration CLI surface"
read_when:
  - Looking up a CLI command or its flags
  - Understanding what the CLI can do
title: "CLI Surface Map"
---

# CLI Surface Map

## Daemon Commands

```bash
openclaw gateway start          # Start the gateway daemon
openclaw gateway stop           # Stop the gateway daemon
openclaw gateway restart        # Restart the gateway daemon
openclaw gateway status         # Check daemon status
```

## Setup & Configuration

```bash
openclaw configure              # Interactive setup wizard
openclaw doctor                 # Diagnose and repair issues
openclaw onboard                # Channel onboarding wizard
```

The configure wizard supports:
- Provider setup (OpenAI, Anthropic, Google, Ollama, etc.)
- Channel setup (Telegram, Discord, Slack, WhatsApp, etc.)
- Model selection and auth profiles
- Workspace configuration

## Config Management

```bash
openclaw config get <path>      # Get a config value
openclaw config set <path> <v>  # Set a config value
openclaw config schema          # Print live JSON Schema
openclaw config schema.lookup   # Lookup schema for a specific path
```

Config file: `~/.openclaw/openclaw.json` (JSON5 format).

## Plugins

```bash
openclaw plugins install <pkg>  # Install a plugin
openclaw plugins list           # List installed plugins
openclaw plugins status         # Plugin health status
```

## Memory

```bash
openclaw memory status [--deep] # Memory system health check
```

## Sessions

```bash
openclaw sessions list          # List active sessions
openclaw sessions clear <key>   # Clear/reset a session
```

Session display shows model, usage, and thinking level.

## Models

```bash
openclaw models list            # List available models
openclaw models providers       # List configured providers
```

## Updates

```bash
openclaw update                 # Check and apply updates
openclaw update --check         # Check only, don't apply
```

Update system:
- Verifies packaged dist inventory
- Prunes stale dist files
- Handles npm global upgrades safely
- Rejects unsafe dist symlinks

## Exec Approvals

```bash
openclaw exec-approvals list    # List pending approvals
openclaw exec-approvals approve # Approve a pending command
openclaw exec-approvals deny    # Deny a pending command
```

## In-Chat Slash Commands

Available in any channel session:

| Command | Purpose |
|---------|---------|
| `/status` | Session status, model, usage |
| `/model <ref>` | Override model for this session |
| `/reasoning` | Toggle reasoning visibility |
| `/verbose` | Toggle verbose output |
| `/trace` | Toggle trace diagnostics |
| `/tts on\|off` | Toggle text-to-speech |
| `/active-memory on\|off\|status` | Toggle active memory |
| `/config set\|get\|unset` | In-chat config edits |
| `/reset` | Reset session transcript |

## QR Code Setup

```bash
openclaw qr                     # Display pairing QR code
```

## Diagnostics

```bash
openclaw doctor                 # Full diagnostic check
openclaw doctor --repair        # Auto-repair issues
```

Doctor handles:
- Channel legacy config migration (with fast-path)
- Plugin blocker detection
- Config hash stale-race prevention
- Credential validation
