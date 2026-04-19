---
summary: "Complete CLI command surface — what each openclaw subcommand does"
read_when:
  - Looking up a CLI command or flag
  - Understanding what CLI tools are available
title: "CLI Surface Map"
---

# CLI Surface Map

## Gateway Lifecycle

| Command | Purpose |
|---------|---------|
| `openclaw gateway start` | Start the gateway daemon |
| `openclaw gateway stop` | Stop the gateway |
| `openclaw gateway restart` | Restart the gateway |
| `openclaw gateway status` | Show gateway status |
| `openclaw gateway run` | Run gateway in foreground |

## Setup & Onboarding

| Command | Purpose |
|---------|---------|
| `openclaw onboard` | Full interactive onboarding flow |
| `openclaw configure` | Interactive config wizard |
| `openclaw setup` | Setup wizard |
| `openclaw doctor` | Diagnose and repair issues |
| `openclaw doctor --fix` | Apply automatic repairs |

## Config Management

| Command | Purpose |
|---------|---------|
| `openclaw config get <path>` | Read a config value |
| `openclaw config set <path> <value>` | Set a config value |
| `openclaw config unset <path>` | Remove a config value |
| `openclaw config schema` | Print live JSON Schema |
| `openclaw config set ... --ref-provider default --ref-source env --ref-id VAR` | Set SecretRef value |

## Channel Management

| Command | Purpose |
|---------|---------|
| `openclaw channels status --probe` | Check channel connectivity |
| `openclaw channels login --channel <name>` | QR login (WhatsApp, WeChat, etc.) |
| `openclaw channels add` | Interactive channel setup |
| `openclaw channels resolve --channel <name> "query"` | Resolve channel entity names |
| `openclaw pairing list <channel>` | List pending pairing codes |
| `openclaw pairing approve <channel> <code>` | Approve a pairing request |

## Model Management

| Command | Purpose |
|---------|---------|
| `openclaw models list` | List available models |
| `openclaw models auth login --provider <name>` | Authenticate with a provider |
| `openclaw models auth status` | Check auth status |
| `openclaw onboard --auth-choice <provider>` | Specific provider auth flow |

## Plugin Management

| Command | Purpose |
|---------|---------|
| `openclaw plugins list` | List installed plugins |
| `openclaw plugins install <pkg>` | Install a plugin |
| `openclaw plugins install <pkg> --force` | Reinstall/update a plugin |

## Browser

| Command | Purpose |
|---------|---------|
| `openclaw browser status` | Check browser status |
| `openclaw browser start` | Start managed browser |
| `openclaw browser stop` | Stop managed browser |
| `openclaw browser open <url>` | Open URL in managed browser |
| `openclaw browser snapshot` | Capture page snapshot |
| `openclaw browser screenshot` | Take screenshot |

## Session Management

| Command | Purpose |
|---------|---------|
| `openclaw sessions list` | List active sessions |
| `openclaw sessions info <id>` | Session details |

## Memory

| Command | Purpose |
|---------|---------|
| `openclaw memory status` | Memory system health |
| `openclaw memory status --deep` | Detailed memory diagnostics |

## Node Management

| Command | Purpose |
|---------|---------|
| `openclaw nodes list` | List paired nodes |
| `openclaw node <id>` | Node details |
| `openclaw devices list` | List connected devices |

## Diagnostics & Utilities

| Command | Purpose |
|---------|---------|
| `openclaw status` | System overview |
| `openclaw health` | Health check |
| `openclaw logs [--follow]` | View logs |
| `openclaw --version` | Print version |
| `openclaw update` | Update OpenClaw |
| `openclaw sandbox` | Sandbox management |
| `openclaw secrets` | Secrets management |
| `openclaw security` | Security audit |
| `openclaw dashboard` | Open web dashboard |
| `openclaw qr` | Generate QR code |

## Cron & Automation

| Command | Purpose |
|---------|---------|
| `openclaw cron list` | List cron jobs |
| `openclaw hooks` | Webhook/hook management |
| `openclaw flows` | Flow management |

## Misc

| Command | Purpose |
|---------|---------|
| `openclaw backup` | Backup state |
| `openclaw reset` | Reset configuration |
| `openclaw completion` | Shell completion setup |
| `openclaw uninstall` | Uninstall OpenClaw |
| `openclaw docs` | Open documentation |