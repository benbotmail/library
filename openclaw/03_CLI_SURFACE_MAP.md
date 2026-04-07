---
summary: "Complete CLI command reference and surface map"
read_when:
  - Working with CLI commands and options
  - Understanding command structure and usage
title: "CLI Surface Map"
sidebarTitle: "CLI Commands"
---

# CLI Surface Map

## Main CLI Interface

### Global Options
```bash
openclaw [command] [options]
```

**Common Options:**
- `--help` - Show help for command
- `--verbose` - Enable verbose logging
- `--debug` - Enable debug logging (alias for --verbose)
- `--json` - JSON output for commands that support it
- `--version` - Show version information

## Command Categories

### 🦞 Gateway Management
```bash
# Gateway lifecycle and health
openclaw gateway [subcommand]

# Run gateway in foreground
openclaw gateway run

# Show gateway service status
openclaw gateway status

# Fetch gateway health
openclaw gateway health

# Call gateway methods directly
openclaw gateway call <method>

# Usage and cost tracking
openclaw gateway usage-cost

# Gateway discovery
openclaw gateway discover

# Remote gateway access
openclaw gateway probe --ssh user@host
```

### ⚙️ Configuration Management
```bash
# Configuration operations
openclaw config [subcommand]

# Get configuration values
openclaw config get [path]

# Set configuration values
openclaw config set <path> <value>

# Unset configuration values  
openclaw config unset <path>

# Show configuration schema
openclaw config schema

# Validate configuration
openclaw config validate

# Configuration file management
openclaw config file <path>
```

### 🔍 System Status & Health
```bash
# System status overview
openclaw status [options]

# Channel health probing
openclaw status --deep

# Usage/cost snapshot
openclaw status --usage

# Full diagnosis (read-only)
openclaw status --all

# JSON output
openclaw status --json
```

### 💬 Session Management
```bash
# List sessions
openclaw sessions [options]

# List all sessions
openclaw sessions

# List sessions for specific agent
openclaw sessions --agent <id>

# Aggregate sessions across agents
openclaw sessions --all-agents

# Filter by recent activity
openclaw sessions --active <minutes>

# Session cleanup
openclaw sessions cleanup [options]

# Dry-run cleanup preview
openclaw sessions cleanup --dry-run

# Force cleanup
openclaw sessions cleanup --enforce

# Fix missing transcript files
openclaw sessions cleanup --fix-missing
```

### 🎯 Background Tasks
```bash
# Task management
openclaw tasks [subcommand]

# List tasks
openclaw tasks list

# Filter tasks by runtime
openclaw tasks --runtime <type>

# Filter tasks by status
openclaw tasks --status <status>

# Task audit
openclaw tasks audit

# Task maintenance
openclaw tasks maintenance

# Show specific task
openclaw tasks show <lookup>

# Cancel running task
openclaw tasks cancel <lookup>

# Set task notification policy
openclaw tasks notify <lookup> <policy>
```

### 📡 Channel Management
```bash
# Channel operations
openclaw channels [subcommand]

# List configured channels
openclaw channels list

# Show channel status
openclaw channels status

# Channel-specific commands
openclaw channels telegram
openclaw channels whatsapp  
openclaw channels discord
openclaw channels slack
openclaw channels signal
```

### 🔐 Security Management
```bash
# Security operations
openclaw security [subcommand]

# Security audit
openclaw security audit

# Policy management
openclaw security policy [options]

# Exec policy configuration
openclaw security exec-policy [options]

# Allowlist/denylist management
openclaw security allowlist
openclaw security denylist
```

### 🔐 Secrets Management
```bash
# Secret operations
openclaw secrets [subcommand]

# List secrets
openclaw secrets list

# Set secret reference
openclaw secrets set <name> --ref-provider <provider> --ref-source <source> --ref-id <id>

# Remove secret
openclaw secrets unset <name>

# Secret providers management
openclaw secrets providers
```

### 📦 Plugin Management
```bash
# Plugin operations
openclaw plugins [subcommand]

# List plugins
openclaw plugins list

# Install plugin
openclaw plugins install <name>

# Update plugin
openclaw plugins update <name>

# Uninstall plugin
openclaw plugins uninstall <name>

# Plugin configuration
openclaw plugins config <name>
```

### 🔄 System Management
```bash
# System operations
openclaw system [subcommand]

# System health check
openclaw system health

# Resource monitoring
openclaw system resources

# Logs management
openclaw system logs [options]

# Backup operations
openclaw system backup [options]
```

### 🤖 Agent Management
```bash
# Agent operations
openclaw agent [subcommand]

# List agents
openclaw agent list

# Show agent status
openclaw agent status <id>

# Agent configuration
openclaw agent config <id>

# Agent management commands
openclaw agent spawn
openclaw agent kill
openclaw agent restart
```

### 📱 Node Management
```bash
# Node operations
openclaw nodes [subcommand]

# List connected nodes
openclaw nodes list

# Node status and health
openclaw nodes status

# Node camera access
openclaw nodes camera <node>

# Node screen recording
openclaw nodes screen <node>

# Node notifications
openclaw nodes notifications <node>

# Node location
openclaw nodes location <node>

# Node command execution
openclaw nodes exec <node> <command>
```

### 🎨 Canvas & UI
```bash
# Canvas operations
openclaw canvas [subcommand]

# Canvas status
openclaw canvas status

# Canvas screenshot
openclaw canvas screenshot

# Canvas evaluation
openclaw canvas eval <javascript>

# Canvas navigation
openclaw canvas navigate <url>
```

### 🕐 Cron & Scheduling
```bash
# Cron operations
openclaw cron [subcommand]

# List cron jobs
openclaw cron list

# Add cron job
openclaw cron add <schedule> <command>

# Update cron job
openclaw cron update <id> [options]

# Remove cron job
openclaw cron remove <id>

# Run cron job manually
openclaw cron run <id>

# Cron job history
openclaw cron runs <id>
```

### 🌐 Web & Browser
```bash
# Browser operations
openclaw browser [subcommand]

# Browser status
openclaw browser status

# Browser actions
openclaw browser open <url>
openclaw browser screenshot
openclaw browser evaluate <javascript>

# Browser tabs management
openclaw browser tabs
openclaw browser focus <tab>
```

## CLI Features

### 🔧 Configuration Set Examples
```bash
# Set basic config
openclaw config set gateway.port 19001

# Set secret reference
openclaw config set channels.telegram.token --ref-provider default --ref-source env --ref-id TELEGRAM_TOKEN

# Set complex configuration
openclaw config set models.glm.apiKey "your-api-key"

# Batch configuration
openclaw config set --batch-file ./config-set.batch.json --dry-run
```

### 📊 Status Options
```bash
# Quick status
openclaw status

# Full diagnosis
openclaw status --all

# Cost usage
openclaw status --usage

# Deep channel probing
openclaw status --deep --timeout 5000

# JSON output
openclaw status --json
```

### 🎯 Task Management
```bash
# List all tasks
openclaw tasks list

# Filter by runtime
openclaw tasks --runtime subagent

# Filter by status
openclaw tasks --status running

# Audit stale tasks
openclaw tasks audit --severity error

# Maintenance preview
openclaw tasks maintenance --dry-run

# Apply maintenance
openclaw tasks maintenance --apply
```

## CLI Workflow Patterns

### Configuration Workflow
```bash
1. Check current config: openclaw config get gateway.port
2. Validate config: openclaw config schema
3. Set new config: openclaw config set gateway.port 19001
4. Restart if needed: openclaw gateway restart
```

### Session Management Workflow
```bash
1. List sessions: openclaw sessions
2. Check specific session: openclaw sessions --agent work
3. Cleanup old sessions: openclaw sessions cleanup --dry-run
4. Apply cleanup: openclaw sessions cleanup --enforce
```

### Channel Health Workflow
```bash
1. Check status: openclaw status
2. Deep probe: openclaw status --deep
3. Usage check: openclaw status --usage
4. Remote access: openclaw gateway probe --ssh user@remote
```

This CLI surface reflects the exact current command structure and options available in OpenClaw as of the current release.