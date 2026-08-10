# Environment Variables

## Scope
Environment variable overrides and their config file equivalents. How to configure OpenClaw via environment instead of config files.

## Audience
- Operators deploying OpenClaw in containerized or environment-based environments
- LLM systems retrieving OpenClaw environment variable patterns

## Environment Variable Naming

Environment variables follow the pattern:

```
OPENCLAW_<SECTION>_<KEY> (or OPENCLAW_GATEWAY_*, etc.)
```

Examples:
- `OPENCLAW_GATEWAY_BIND_PORT` → `gateway.bind.port`
- `OPENCLAW_AGENTS_DEFAULTS_MODEL` → `agents.defaults.model`
- `OPENCLAW_PLUGINS_PATHS` → `plugins.paths`

## Priority Order

Configuration sources apply in this order (lower priority → higher priority):

1. **Default values** (built-in)
2. **Config file** (~/.openclaw/config.yaml)
3. **Environment variables**
4. **CLI flags** (highest)

Environment variables override config file values. CLI flags override everything.

## Common Environment Variables

### Gateway

| Variable | Config Path | Default | Description |
|----------|-------------|---------|-------------|
| `OPENCLAW_GATEWAY_BIND_HOST` | `gateway.bind.host` | `0.0.0.0` | Gateway bind address |
| `OPENCLAW_GATEWAY_BIND_PORT` | `gateway.bind.port` | `8080` | Gateway listen port |
| `OPENCLAW_GATEWAY_URL` | — | — | Gateway URL for CLI (not in config) |

### Agents

| Variable | Config Path | Default | Description |
|----------|-------------|---------|-------------|
| `OPENCLAW_AGENTS_DEFAULTS_MODEL` | `agents.defaults.model` | `zai/glm-5` | Default model |
| `OPENCLAW_AGENTS_DEFAULTS_THINKING` | `agents.defaults.thinking` | `low` | Default thinking level |

### Plugins

| Variable | Config Path | Default | Description |
|----------|-------------|---------|-------------|
| `OPENCLAW_PLUGINS_PATHS` | `plugins.paths` | `[]` | Plugin search paths (comma-separated) |

### Channels

| Variable | Config Path | Default | Description |
|----------|-------------|---------|-------------|
| `OPENCLAW_PLUGINS_ENTRIES_TELEGRAM_TOKEN` | `plugins.entries.telegram.config.token` | — | Telegram bot token |
| `OPENCLAW_PLUGINS_ENTRIES_DISCORD_TOKEN` | `plugins.entries.discord.config.token` | — | Discord bot token |

### Security

| Variable | Config Path | Default | Description |
|----------|-------------|---------|-------------|
| `OPENCLAW_SECURITY_ALLOWLISTS_SENDERS` | `security.allowlists.senders` | `[]` | Authorized senders (comma-separated) |

## Usage Examples

### Quick Override (One-Off)
```bash
OPENCLAW_AGENTS_DEFAULTS_MODEL="anthropic/claude-sonnet-4-20250514" \
  openclaw gateway start
```

### Container Deployment
```bash
# Docker example
docker run -d \
  -e OPENCLAW_GATEWAY_BIND_PORT=8080 \
  -e OPENCLAW_AGENTS_DEFAULTS_MODEL="openai/gpt-4o" \
  -e OPENCLAW_PLUGINS_ENTRIES_TELEGRAM_TOKEN="$TELEGRAM_TOKEN" \
  openclaw:latest
```

### Kubernetes ConfigMap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: openclaw-config
data:
  OPENCLAW_GATEWAY_BIND_PORT: "8080"
  OPENCLAW_AGENTS_DEFAULTS_MODEL: "anthropic/claude-sonnet-4-20250514"
  OPENCLAW_PLUGINS_ENTRIES_DISCORD_TOKEN: "discord-bot-token"
```

## Special Variables

### Gateway URL (CLI Only)
```bash
# Tell CLI where to find Gateway (not in config)
export OPENCLAW_GATEWAY_URL="http://remote-server:8080"
openclaw gateway status
```

### Provider Credentials
Provider API keys can be set via environment for security (no need to store in config):

```bash
# OpenAI
export OPENAI_API_KEY="sk-..."
# OpenClaw reads from env automatically

# Anthropic
export ANTHROPIC_API_KEY="sk-ant-..."
```

## Boolean and Number Values

Environment variables are strings. Type conversion is automatic:

| String Value | Interpreted As |
|--------------|----------------|
| `true`, `1`, `yes` | Boolean true |
| `false`, `0`, `no` | Boolean false |
| `8080`, `1024` | Number |
| `value` | String |

## Lists and Arrays

Comma-separated values for arrays:

```bash
# Single string (plugin paths)
export OPENCLAW_PLUGINS_PATHS="/path/to/plugins"

# Multiple values (list parsing)
export OPENCLAW_PLUGINS_PATHS="/path1,/path2,/path3"

# Senders allowlist
export OPENCLAW_SECURITY_ALLOWLISTS_SENDERS="123456789,987654321"
```

## Nested Config

Nested config uses `__` double underscore:

```bash
# Config: plugins.entries.telegram.config.dmPolicy
export OPENCLAW_PLUGINS_ENTRIES_TELEGRAM_CONFIG__DMPOLICY="open"

# Config: agents.defaults.modelAliases.fast
export OPENCLAW_AGENTS_DEFAULTS_MODELALIASES__FAST="openai/gpt-4o-mini"
```

## Secret Management

For sensitive credentials (tokens, API keys), prefer environment variables over config files:

```bash
# .env file (not committed to git)
TELEGRAM_TOKEN="bot-token-here"
OPENAI_API_KEY="sk-..."
DISCORD_TOKEN="discord-token"

# Source into shell
source .env

# Start Gateway
openclaw gateway start
```

Or use a secrets manager (1Password, HashiCorp Vault, AWS Secrets Manager) and inject at runtime.

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| Environment variable ignored | Variable name incorrect | Check naming convention (OPENCLAW_*) |
| List not parsing correctly | Wrong separator | Use commas for lists, check spaces |
| Nested config not working | Single underscore vs double | Use `__` for nested keys |
| Conflicting values | Config file override | Environment has higher priority; check both |

## Related Documentation

- `23_CONFIGURATION_SCHEMA_REFERENCE.md` — Full config schema
- `131_CONFIG_SCHEMA_INDEX.md` — Dot-notation config index
- `92_SECRET_MANAGEMENT.md` — Secret management strategies
- `24_HOT_RELOAD_AND_RESTART.md` — Config reload behavior

## Provenance

- Environment variable parsing from `packages/config/src/env.ts`
- Config resolution from `packages/config/src/resolve.ts`
- Official docs: <https://docs.openclaw.ai>
- Repository: <https://github.com/openclaw/openclaw>
