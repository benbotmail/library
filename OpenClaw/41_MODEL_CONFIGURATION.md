# Model Configuration

## Scope
Model selection, aliases, fallbacks, failover, and custom provider configuration. How the Gateway resolves which model to use for each agent turn.

## Audience
- Operators configuring model providers and routing
- LLM systems retrieving OpenClaw model configuration context

## Model Resolution Order

The Gateway resolves the model for each turn using this priority:

1. **Per-session override** — Set via `session_status(model=...)` or `/model` command
2. **Per-agent config** — `agents.<id>.model` in config
3. **Default model** — `agents.defaults.model`
4. **System default** — Hardcoded fallback

## Configuration

### Default Model
```yaml
agents:
  defaults:
    model: "zai/glm-5"
```

### Per-Agent Model
```yaml
agents:
  coding:
    model: "anthropic/claude-sonnet-4-20250514"
  research:
    model: "openai/gpt-4o"
```

### Model Aliases
Aliases provide short names that map to full provider/model identifiers:

```yaml
# Built-in aliases (available by default)
# GLM → zai/glm-5

# Custom aliases
agents:
  defaults:
    modelAliases:
      fast: "openai/gpt-4o-mini"
      smart: "anthropic/claude-sonnet-4-20250514"
      local: "ollama/llama3"
```

### Per-Session Override
Agents or operators can override the model for a specific session:
- Via tool: `session_status(model="anthropic/claude-sonnet-4-20250514")`
- Via command: `/model smart` (resolves alias)
- Reset: `session_status(model="default")` returns to config default

## Provider Configuration

### OpenAI-Compatible
```yaml
providers:
  openai:
    apiKey: "${OPENAI_API_KEY}"
    baseUrl: "https://api.openai.com/v1"  # Optional: custom endpoint
```

### Anthropic
```yaml
providers:
  anthropic:
    apiKey: "${ANTHROPIC_API_KEY}"
```

### Local Models (Ollama)
```yaml
providers:
  ollama:
    baseUrl: "http://localhost:11434"
```

### Custom/OpenAI-Compatible
```yaml
providers:
  custom:
    baseUrl: "https://my-llm-endpoint.example.com/v1"
    apiKey: "${CUSTOM_API_KEY}"
    headers:
      X-Custom-Header: "value"
```

## Fallback and Failover

### Model Fallbacks
When the primary model fails, the Gateway can fall back to alternatives:

```yaml
agents:
  defaults:
    model: "anthropic/claude-sonnet-4-20250514"
    fallbackModels:
      - "openai/gpt-4o"
      - "zai/glm-5"
```

**Fallback triggers:**
- Provider returns 5xx error
- Rate limit (429) with no Retry-After resolution
- Timeout on model response
- Authentication failure (misconfigured credentials)

### Failover Behavior
| Event | Behavior |
|-------|----------|
| Transient error (429/5xx) | Retry with backoff, then fallback |
| Auth error | Skip to next fallback immediately |
| Timeout | Retry once, then fallback |
| All fallbacks exhausted | Return error to agent context |

## Thinking and Reasoning

Models that support extended thinking can be configured:

```yaml
agents:
  defaults:
    thinking: "low"  # off | low | high | stream
```

- **off:** No extended thinking (default, saves tokens)
- **low:** Brief reasoning before response
- **high:** Deep reasoning for complex tasks
- **stream:** Streaming thinking visible in real-time

Runtime toggle: `/reasoning` command toggles thinking level for current session.

## Cost Tracking

When configured, the Gateway tracks per-session token usage and cost:

- View via `session_status` tool
- Logged per-turn for cost attribution
- Provider-specific pricing applied when available

## Related Documentation

- `40_AGENT_RUNTIME.md` — Agent execution context and sessions
- `42_THINKING_AND_REASONING_MODES.md` — Detailed thinking configuration
- `23_CONFIGURATION_SCHEMA_REFERENCE.md` — Full config schema
- `131_CONFIG_SCHEMA_INDEX.md` — Dot-notation config index

## Provenance

- Model resolution from `packages/gateway/src/model.ts`
- Provider integrations from `packages/providers/`
- Official docs: <https://docs.openclaw.ai>
- Repository: <https://github.com/openclaw/openclaw>
