# OpenClaw Quick Reference

> Current as of 2026-08-13 (upstream `0926d56cbf9`).

## Essential Commands

```bash
openclaw onboard              # Guided setup
openclaw gateway              # Start gateway
openclaw gateway status       # Check gateway
openclaw gateway restart      # Restart gateway
openclaw doctor               # Diagnose issues
openclaw doctor --fix         # Auto-repair config
openclaw logs --follow        # Live logs
```

## Config Quick Set

```bash
openclaw config get agents.defaults.model.primary
openclaw config set agents.defaults.heartbeat.every "2h"
openclaw config set agents.defaults.sandbox.mode "non-main"
openclaw configure             # Interactive wizard
```

## Channel Setup Patterns

```json5
// Telegram
{ channels: { telegram: { enabled: true, botToken: "...", dmPolicy: "pairing" } } }

// WhatsApp
{ channels: { whatsapp: { dmPolicy: "pairing", allowFrom: ["+15551234567"] } } }

// Discord
{ channels: { discord: { enabled: true, botToken: "...", applicationId: "..." } } }
```

## Streaming Quick Config

```json5
// Telegram: default progress mode (tool progress in draft, final as new message)
{ channels: { telegram: { streaming: { mode: "progress" } } } }

// Telegram: stream answer text into preview (legacy behavior)
{ channels: { telegram: { streaming: { mode: "partial" } } } }

// Discord: enable progress preview
{ channels: { discord: { streaming: { mode: "progress" } } } }

// Any channel: disable preview
{ channels: { telegram: { streaming: { mode: "off" } } } }
```

## Heartbeat Quick Config

```json5
{
  agents: {
    defaults: {
      heartbeat: {
        every: "30m",
        target: "owner",
        directPolicy: "allow",
      },
    },
  },
}
```

## Sandbox Quick Config

```json5
{
  agents: {
    defaults: {
      sandbox: {
        mode: "non-main",     // sandbox all sessions except main
        backend: "docker",
        scope: "session",
        workspaceAccess: "ro",
      },
    },
  },
}
```

## Pairing

```bash
openclaw pairing list <channel>
openclaw pairing approve <channel> <code>
```

## Model Switching

```
/model anthropic/claude-opus-4-6    # In chat
openclaw agent --model zai/glm-5    # CLI
```

## Session Management

```bash
openclaw sessions                    # List sessions
openclaw sessions --json             # JSON output
```
