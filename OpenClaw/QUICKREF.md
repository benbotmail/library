# OpenClaw Quick Reference Card

> Essential commands and patterns

## Installation

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

## Gateway

```bash
openclaw gateway                    # Start gateway
openclaw gateway status             # Check status
openclaw gateway --port 18789       # Custom port
```

## Configuration

```bash
openclaw config get <path>          # Get value
openclaw config set <path> <value>  # Set value
openclaw config unset <path>        # Remove value
openclaw configure                  # Interactive wizard
```

## Channels

```bash
openclaw channels login             # Login to channel
openclaw channels status --probe    # Check connections
openclaw pairing list <channel>     # View pending
openclaw pairing approve <ch> <code> # Approve sender
```

## Messaging

```bash
openclaw message send --to +123 --message "Hello"
openclaw message read --from +123
```

## Agent

```bash
openclaw agent --message "Hello"    # Chat with agent
openclaw agent --thinking high      # Enable thinking
```

## Sessions

```bash
openclaw sessions                   # List sessions
openclaw sessions cleanup --dry-run # Preview cleanup
```

## Diagnostics

```bash
openclaw doctor                     # Health check
openclaw logs --follow              # Live logs
openclaw status                     # System status
```

## Config File

`~/.openclaw/openclaw.json`

```json5
{
  agents: {
    defaults: {
      workspace: "~/.openclaw/workspace",
      model: { primary: "anthropic/claude-sonnet-4-5" },
    },
  },
  channels: {
    telegram: {
      enabled: true,
      botToken: "123:abc",
      dmPolicy: "pairing",
    },
  },
  session: {
    dmScope: "main",
    maintenance: { mode: "enforce" },
  },
}
```

## Chat Commands

| Command | Action |
|---------|--------|
| `/new` | Reset session |
| `/status` | Session info |
| `/compact` | Summarize context |
| `/stop` | Abort current run |
| `/send on/off` | Toggle delivery |

## Key URLs

- Docs: https://docs.openclaw.ai
- Repo: https://github.com/openclaw/openclaw
- Discord: https://discord.gg/clawd
- Control UI: http://127.0.0.1:18789
