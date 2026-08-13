# Getting Started Route

> End-to-end setup from first install to messaging an agent

## Prerequisites

- Node.js 18+ (v22 recommended)
- An AI provider API key (OpenAI, Anthropic, Google, etc.)
- A messaging platform account (Telegram, Discord, WhatsApp, etc.)

## Installation

```bash
# Install globally
npm install -g openclaw@latest

# Verify
openclaw --version
```

## Onboarding

```bash
# Guided setup wizard
openclaw onboard --install-daemon
```

The onboarding wizard:
1. Creates `~/.openclaw/openclaw.json` with safe defaults
2. Prompts for AI provider API keys
3. Optionally installs the Gateway as a system daemon
4. Sets up the workspace at `~/.openclaw/workspace`

## Configuration

### Minimal config

```json5
{
  agents: {
    defaults: {
      workspace: "~/.openclaw/workspace",
      model: "anthropic/claude-sonnet-4-6",
    },
  },
  channels: {
    telegram: {
      enabled: true,
      botToken: "your-bot-token",
      dmPolicy: "pairing",
    },
  },
}
```

### Provider keys

Set API keys via environment variables or inline config:

```json5
{
  env: {
    vars: {
      ANTHROPIC_API_KEY: "sk-ant-...",
      OPENAI_API_KEY: "sk-...",
    },
  },
}
```

Or use env var substitution in config values:

```json5
{
  models: {
    providers: {
      custom: { apiKey: "${CUSTOM_API_KEY}" },
    },
  },
}
```

## Starting the Gateway

```bash
# Start (foreground)
openclaw gateway

# Start as daemon
openclaw gateway start

# Check status
openclaw gateway status
```

Default control plane: `ws://127.0.0.1:18789`

## Channel Setup

### Telegram (ships in core)

1. Create a bot via [@BotFather](https://t.me/BotFather)
2. Copy the bot token
3. Add to config:
   ```json5
   {
     channels: {
       telegram: {
         enabled: true,
         botToken: "123456:ABC-DEF...",
       },
     },
   }
   ```
4. Restart or let hot-reload pick up the change
5. Message your bot on Telegram
6. Approve pairing:
   ```bash
   openclaw pairing list telegram
   openclaw pairing approve telegram <CODE>
   ```

### Other channels

Install as plugins:
```bash
openclaw plugins install @openclaw/discord
openclaw plugins install @openclaw/whatsapp
```

## Workspace Bootstrap

The Gateway auto-creates workspace files on first run:
- `AGENTS.md` — Agent behavior instructions
- `SOUL.md` — Agent personality
- `IDENTITY.md` — Agent identity card
- `USER.md` — Info about the human
- `BOOTSTRAP.md` — First-run instructions (delete after)

Disable with `agents.defaults.skipBootstrap: true`.

## Verification

```bash
# Check system health
openclaw doctor

# Check gateway status
openclaw gateway status

# List sessions
openclaw sessions list

# Send a test message via CLI
openclaw message send --channel telegram --text "Hello!"
```

## Next Steps

- Configure tool profiles (`tools.profile`)
- Set up cron jobs (`openclaw automations`)
- Pair mobile nodes (`openclaw pairing`)
- Customize workspace files (`AGENTS.md`, `SOUL.md`)
- Explore CLI commands (`openclaw help`)
