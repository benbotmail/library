# Command Line Reference

All CLI commands for managing OpenClaw, the Gateway, channels, agents, and configuration.

Run `openclaw --help` or `openclaw <command> --help` for usage details.

## Gateway commands

### openclaw gateway
Start the Gateway daemon.

```bash
openclaw gateway [options]
```

**Options:**
- `-p, --port <port>` — Port to listen on (default: 18789)
- `-H, --host <host>` — Host to bind to (default: 127.0.0.1)
- `-v, --verbose` — Enable verbose logging
- `--config <path>` — Path to config file (default: ~/.openclaw/openclaw.json)
- `--workspace <path>` — Path to workspace (default: ~/.openclaw/workspace)
- `--dry-run` — Validate configuration without starting

**Examples:**
```bash
# Start on default port
openclaw gateway

# Start on custom port
openclaw gateway --port 8080

# Start with verbose logging
openclaw gateway --verbose

# Validate config without starting
openclaw gateway --dry-run
```

### openclaw status
Check Gateway and system status.

```bash
openclaw status
```

**Output shows:**
- Gateway running status
- Active sessions
- Channel connections
- Model configuration
- Resource usage

### openclaw doctor
Run diagnostics and health checks.

```bash
openclaw doctor [options]
```

**Options:**
- `--fix` — Apply automatic fixes
- `--yes` — Apply fixes without confirmation
- `--json` — Output as JSON

**Checks:**
- Node.js version
- Config file validity
- Gateway status
- Channel connections
- Workspace permissions
- API key presence
- Plugin loading

**Example:**
```bash
# Run diagnostics
openclaw doctor

# Auto-fix issues
openclaw doctor --fix
```

### openclaw logs
View Gateway and session logs.

```bash
openclaw logs [options]
```

**Options:**
- `--tail <lines>` — Show last N lines (default: 50)
- `--follow` — Follow log output (like tail -f)
- `--session <key>` — Show logs for specific session
- `--channel <name>` — Show logs for specific channel
- `--level <level>` — Filter by log level (error, warn, info, debug)
- `--since <time>` — Show logs since timestamp
- `--until <time>` — Show logs until timestamp

**Examples:**
```bash
# Show last 100 lines
openclaw logs --tail 100

# Follow logs in real-time
openclaw logs --follow

# Show logs for specific session
openclaw logs --session whatsapp:+15555550123

# Show error logs only
openclaw logs --level error
```

### openclaw restart
Restart the Gateway daemon.

```bash
openclaw restart
```

**Use:** Apply configuration changes or recover from errors.

### openclaw stop
Stop the Gateway daemon.

```bash
openclaw stop
```

## Agent commands

### openclaw agent
Send a message to an agent directly from CLI.

```bash
openclaw agent [options]
```

**Options:**
- `-m, --message <text>` — Message to send
- `-s, --session <key>` — Session key (default: main session)
- `--model <model>` — Override model for this turn
- `--thinking <level>` — Set thinking level (off, on, stream)
- `--thread` — Run in Discord thread (ACP mode only)

**Examples:**
```bash
# Send message to agent
openclaw agent --message "Hello from CLI"

# Use specific model
openclaw agent --message "Help me code" --model "anthropic/claude-opus-4-6"

# Enable reasoning
openclaw agent --message "Analyze this" --thinking on
```

### openclaw sessions
Manage sessions.

```bash
openclaw sessions <action> [options]
```

**Actions:**
- `list` — List sessions
- `history <session-key>` — Show session history
- `kill <session-key>` — Kill a session

**Options (list):**
- `--active-minutes <min>` — Filter by recent activity
- `--kinds <types>` — Filter by session kinds
- `--limit <num>` — Limit results

**Examples:**
```bash
# List all sessions
openclaw sessions list

# List active sessions from last hour
openclaw sessions list --active-minutes 60

# Show session history
openclaw sessions history whatsapp:+15555550123

# Kill a session
openclaw sessions kill telegram:123456789
```

### openclaw subagents
Manage sub-agents.

```bash
openclaw subagents <action> [options]
```

**Actions:**
- `list` — List sub-agents
- `kill <agent-id>` — Kill a sub-agent
- `steer <agent-id> --message <text>` — Send message to sub-agent

**Examples:**
```bash
# List sub-agents
openclaw subagents list

# Kill a sub-agent
openclaw subagents kill agent-abc123

# Steer a sub-agent
openclaw subagents steer agent-abc123 --message "Focus on the first file"
```

## Channel commands

### openclaw channels
Manage channel connections.

```bash
openclaw channels <action> [options]
```

**Actions:**
- `login <provider>` — Login to a channel
- `logout <provider>` — Logout from a channel
- `status` — Show channel status
- `list` — List available channels

**Providers:**
- `whatsapp`
- `telegram`
- `discord`
- `slack`
- `signal`
- `imessage` (macOS only)
- `googlechat`
- `irc`
- `msteams`
- `matrix`
- `feishu`
- `line`
- `mattermost`
- `nextcloudtalk`
- `nostr`
- `synologychat`
- `tlon`
- `twitch`
- `zalo`
- `webchat`

**Examples:**
```bash
# Login to WhatsApp
openclaw channels login whatsapp

# Login to Telegram with bot token
openclaw channels login telegram --bot-token "123456:ABC..."

# Show channel status
openclaw channels status

# Logout from Discord
openclaw channels logout discord
```

### openclaw message
Send messages to channels.

```bash
openclaw message send [options]
```

**Options:**
- `-c, --channel <name>` — Channel type (telegram, discord, etc.)
- `-t, --to <target>` — Target channel/user ID or name
- `-m, --message <text>` — Message content
- `-f, --file <path>` — Attach file
- `--reply-to <id>` — Reply to message ID

**Examples:**
```bash
# Send message to Telegram user
openclaw message send --channel telegram --to 123456789 --message "Hello!"

# Send message to Discord channel
openclaw message send --channel discord --to 123456789 --message "Announcement"

# Send with file attachment
openclaw message send --channel telegram --to 123456789 --message "Check this" --file document.pdf
```

## Configuration commands

### openclaw config
Manage configuration.

```bash
openclaw config <action> [path] [value]
```

**Actions:**
- `get <path>` — Get config value
- `set <path> <value>` — Set config value
- `unset <path>` — Remove config value
- `edit` — Open config in editor
- `schema` — Show full schema
- `schema.lookup <path>` — Lookup schema for path

**Examples:**
```bash
# Get config value
openclaw config get agents.defaults.model.primary

# Set config value
openclaw config set channels.whatsapp.dmPolicy allowlist

# Remove config value
openclaw config unset channels.whatsapp.allowFrom

# Edit config in editor
openclaw config edit

# Show full schema
openclaw config schema

# Lookup specific path
openclaw config.schema.lookup channels.whatsapp.dmPolicy
```

### openclaw configure
Interactive configuration wizard.

```bash
openclaw configure
```

**Prompts for:**
- Model selection and API keys
- Channel setup
- Workspace location
- Security settings

### openclaw onboard
Complete onboarding flow for new installs.

```bash
openclaw onboard [options]
```

**Options:**
- `--install-daemon` — Install Gateway as system service
- `--skip-daemon` — Skip daemon installation

**What it does:**
- Creates directory structure
- Sets up workspace
- Prompts for model and channel setup
- Installs daemon (systemd/launchd)

## Secret management

### openclaw secret
Manage secrets (API keys, tokens, etc.).

```bash
openclaw secret <action> [key] [value]
```

**Actions:**
- `set <key> <value>` — Set secret
- `get <key>` — Get secret
- `unset <key>` — Remove secret
- `list` — List all secrets (values hidden)

**Examples:**
```bash
# Set OpenAI API key
openclaw secret set openai.apiKey sk-...

# Get secret
openclaw secret get openai.apiKey

# List all secrets
openclaw secret list

# Remove secret
openclaw secret unset openai.apiKey
```

**Secret storage:** Depends on `security.secrets.backend` configuration.

## Diagnostic commands

### openclaw health
Quick health check.

```bash
openclaw health
```

**Output:**
- Gateway running status
- Channel connectivity
- Recent error count
- System resources

### openclaw version
Show version information.

```bash
openclaw version
```

**Output:**
- OpenClaw version
- Git commit hash
- Node.js version
- OS information

### openclaw info
Show system and configuration information.

```bash
openclaw info
```

**Output:**
- Gateway status
- Config file location
- Workspace location
- Active sessions
- Channel connections
- Model configuration

## Utility commands

### openclaw update
Update OpenClaw to latest version.

```bash
openclaw update [options]
```

**Options:**
- `--channel <channel>` — Channel to update to (stable, beta, dev)
- `--dry-run` — Check for updates without installing

**Channels:**
- `stable` — Tagged releases (default)
- `beta` — Prerelease tags
- `dev` — Moving head of `main`

**Examples:**
```bash
# Update to latest stable
openclaw update

# Update to beta channel
openclaw update --channel beta

# Check for updates without installing
openclaw update --dry-run
```

### openclaw update-check
Check for available updates.

```bash
openclaw update-check
```

**Output:** Available version, current version, update command.

### openclaw help
Show help for commands.

```bash
openclaw help [command]
```

**Examples:**
```bash
# Show main help
openclaw help

# Show help for specific command
openclaw help gateway
openclaw help config
```

## Cron commands

### openclaw cron
Manage cron jobs.

```bash
openclaw cron <action> [options]
```

**Actions:**
- `list` — List cron jobs
- `create <name>` — Create cron job
- `enable <name>` — Enable cron job
- `disable <name>` — Disable cron job
- `delete <name>` — Delete cron job

**Examples:**
```bash
# List cron jobs
openclaw cron list

# Create cron job (interactive)
openclaw cron create morning-check

# Enable cron job
openclaw cron enable morning-check

# Delete cron job
openclaw cron delete morning-check
```

**See:** `70_CRON_AND_SCHEDULING.md` for cron job configuration.

## Plugin commands

### openclaw plugins
Manage plugins.

```bash
openclaw plugins <action> [options]
```

**Actions:**
- `list` — List installed plugins
- `install <name>` — Install plugin
- `uninstall <name>` — Uninstall plugin
- `update <name>` — Update plugin
- `enable <name>` — Enable plugin
- `disable <name>` — Disable plugin

**Examples:**
```bash
# List installed plugins
openclaw plugins list

# Install plugin
openclaw plugins install openclaw-skill-coding-agent

# Uninstall plugin
openclaw plugins uninstall openclaw-skill-coding-agent

# Enable plugin
openclaw plugins enable coding-agent
```

## Web UI commands

### openclaw web
Open web Control UI.

```bash
openclaw web [options]
```

**Options:**
- `--url <url>` — Custom Gateway URL
- `--open` — Automatically open in browser

**Default URL:** http://127.0.0.1:18789/

**See:** `docs/web/control-ui.md` for Web UI documentation.

## Environment variables

OpenClaw respects these environment variables:

- `OPENCLAW_CONFIG` — Path to config file (overrides default)
- `OPENCLAW_WORKSPACE` — Path to workspace (overrides default)
- `OPENCLAW_PORT` — Port to listen on (overrides config)
- `OPENCLAW_HOST` — Host to bind to (overrides config)
- `OPENCLAW_LOG_LEVEL` — Log level (error, warn, info, debug, trace)
- `OPENCLAW_NO_COLOR` — Disable colored output
- `NODE_OPTIONS` — Node.js options (e.g., `--max-old-space-size=4096`)

**Example:**
```bash
# Set custom config location
OPENCLAW_CONFIG=/opt/openclaw/config.json openclaw gateway

# Increase Node.js memory limit
NODE_OPTIONS=--max-old-space-size=4096 openclaw gateway

# Disable colors for logging
OPENCLAW_NO_COLOR=1 openclaw logs
```

## Exit codes

- `0` — Success
- `1` — General error
- `2` — Invalid usage
- `3` — Config validation failed
- `4` — Permission denied
- `5` — Command not found
- `6` — Network error
- `7` — Timeout

## Provenance
- **Source:** `src/cli/`, README.md
- **Last validated:** 2026-03-18 (against openclaw@latest from GitHub)
