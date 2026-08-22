# Getting Started Route

This guide provides an end-to-end setup path from zero to messaging an agent.

## Prerequisites

- **Node.js**: Version 22+ (Node 24 recommended)
- **OS**: macOS, Linux, or Windows (WSL2 strongly recommended)
- **Terminal**: For CLI commands
- **API key**: For your chosen LLM provider (OpenAI, Anthropic, etc.)

## Step 1: Install OpenClaw

**Option A: Global npm install (recommended)**
```bash
npm install -g openclaw@latest
# or
pnpm add -g openclaw@latest
```

**Option B: From source (development)**
```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw
pnpm install
pnpm build
./openclaw.mjs --help
```

**Verify installation:**
```bash
openclaw --version
```

**Expected output:**
```
openclaw v2026.03.18 (git: abc123)
```

## Step 2: Run onboarding (optional but recommended)

The onboarding wizard configures essential settings interactively.

```bash
openclaw onboard --install-daemon
```

**What it does:**
- Creates `~/.openclaw/` directory structure
- Sets up the workspace at `~/.openclaw/workspace`
- Installs the Gateway daemon (systemd or launchd)
- Prompts for channel setup (WhatsApp, Telegram, etc.)
- Creates a basic `openclaw.json` config

**If you skip onboarding:** The Gateway will use safe defaults and create directories on first run.

## Step 3: Configure your model

The Gateway needs access to an LLM provider.

**Option A: Interactive configuration**
```bash
openclaw configure
# Follow prompts to set up API keys
```

**Option B: Direct config edit**
```bash
# Edit the config file
~/.openclaw/openclaw.json
```

**Minimal config example:**
```json5
{
  agents: {
    defaults: {
      model: {
        primary: "openai/gpt-4o",
      },
      models: {
        "openai/gpt-4o": {
          apiKey: "sk-...",  // Set via environment or openclaw secret
        },
      },
    },
  },
}
```

**Set API key via CLI (recommended):**
```bash
openclaw secret set openai.apiKey sk-...
```

**Verify model configuration:**
```bash
openclaw config get agents.defaults.model.primary
```

## Step 4: Set up a channel

Choose one or more channels to connect. Common options:

### Option A: WhatsApp (most popular)
```bash
openclaw channels login whatsapp
```

**What happens:**
1. Terminal displays a QR code
2. Scan with WhatsApp on your phone
3. Gateway links to your WhatsApp account
4. Verify with test message

**Test:**
```bash
openclaw channels status
```

**Expected output:**
```
✓ WhatsApp: Connected (+15555550123)
```

### Option B: Telegram
```bash
# Create a bot via @BotFather on Telegram
# Get your bot token

openclaw channels login telegram --bot-token "123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11"
```

**Test:**
```bash
openclaw channels status
```

### Option C: WebChat (no auth required)
```bash
openclaw gateway --port 18789
```

Then open [http://127.0.0.1:18789/](http://127.0.0.1:18789/) in your browser.

## Step 5: Start the Gateway

**Option A: Start manually**
```bash
openclaw gateway --port 18789
```

**Expected output:**
```
Gateway started on http://127.0.0.1:18789/
Listening for messages...
```

**Option B: Start daemon (installed via onboarding)**
```bash
# Linux/systemd
systemctl --user start openclaw
systemctl --user enable openclaw  # Start on boot

# macOS/launchd
launchctl start openclaw
```

**Check Gateway status:**
```bash
openclaw status
```

## Step 6: Send your first message

### Via WhatsApp
1. Open WhatsApp on your phone
2. Find the Gateway (listed as "OpenClaw" or similar)
3. Send a message: "Hello from OpenClaw!"

### Via Telegram
1. Find your bot (e.g., @YourBotName)
2. Send a message: "Hello from OpenClaw!"

### Via WebChat
1. Open [http://127.0.0.1:18789/](http://127.0.0.1:18789/)
2. Start chatting in the web interface

### Via CLI
```bash
openclaw agent --message "Hello from OpenClaw!"
```

**Expected response:**
The agent replies using the configured model with access to tools (exec, browser, etc.).

## Step 7: Verify everything works

**Run diagnostics:**
```bash
openclaw doctor
```

**Check for:**
- ✓ Gateway running
- ✓ Channel connected
- ✓ Model configured
- ✓ Workspace accessible
- ✓ No config errors

**If issues:** See `110_TROUBLESHOOTING_ENTRY_POINTS.md`

## Common first-time tasks

### Set up WhatsApp DM policy (restrict access)
```bash
openclaw config set channels.whatsapp.dmPolicy allowlist
openclaw config set channels.whatsapp.allowFrom '["+15555550123"]'
```

### Enable group chat mentions
```json5
{
  channels: {
    whatsapp: {
      groups: {
        "*": { groupPolicy: "requireMention" },
      },
    },
  },
  messages: {
    groupChat: {
      mentionPatterns: ["@openclaw", "@bot"],
    },
  },
}
```

### Pair a mobile device (iOS/Android)
1. Install OpenClaw mobile app on your device
2. In the app, enter your Gateway URL (e.g., `http://your-server:18789`)
3. Scan QR code or enter pairing code
4. Verify in Gateway logs or web UI

**See `54_NODE_PAIRING_AND_CANVAS.md` for details.**

## Next steps

After basic setup:

- **Explore channels**: Set up additional channels (Discord, Slack, etc.) — see `30_CHANNEL_OVERVIEW.md`
- **Configure models**: Set up fallbacks and aliases — see `41_MODEL_CONFIGURATION.md`
- **Set up skills**: Install skills for specialized workflows — see `63_SKILLS_SYSTEM.md`
- **Configure automation**: Set up cron jobs and hooks — see `70_CRON_AND_SCHEDULING.md`
- **Secure access**: Review security settings — see `90_SECURITY_MODEL.md`

## Troubleshooting common issues

### Issue: Gateway won't start
**Check:**
```bash
openclaw doctor
```

**Common causes:**
- Invalid config (syntax error, unknown keys)
- Missing dependencies (Node.js version too old)
- Port already in use (try `--port 18790`)

### Issue: Channel not connecting
**Check:**
```bash
openclaw channels status
```

**WhatsApp:** Re-scan QR code (link expires periodically)
**Telegram:** Verify bot token is correct
**General:** Check internet connection

### Issue: Agent not responding
**Check:**
```bash
openclaw config get agents.defaults.model.primary
openclaw secret list
```

**Common causes:**
- Missing or invalid API key
- Model ID incorrect
- Quota exceeded or API down

## Provenance
- **Source:** docs/start/getting-started.md, README.md
- **Last validated:** 2026-03-18 (against openclaw@latest from GitHub)
