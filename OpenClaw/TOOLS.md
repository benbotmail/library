# OpenClaw Tools Reference

> Built-in agent tools for automation, messaging, and control

## Tool Control

### Allow/Deny Lists

```json5
{
  tools: {
    allow: ["group:fs", "browser"],   // Whitelist
    deny: ["exec"],                    // Blacklist (wins over allow)
    profile: "coding",                 // Base profile
  },
}
```

### Tool Profiles

| Profile | Tools Included |
|---------|----------------|
| `minimal` | `session_status` only |
| `coding` | `group:fs`, `group:runtime`, `group:sessions`, `group:memory`, `image` |
| `messaging` | `group:messaging`, `sessions_*`, `session_status` |
| `full` | All tools (default) |

### Tool Groups

| Group | Tools |
|-------|-------|
| `group:runtime` | `exec`, `bash`, `process` |
| `group:fs` | `read`, `write`, `edit`, `apply_patch` |
| `group:sessions` | `sessions_list`, `sessions_history`, `sessions_send`, `sessions_spawn`, `session_status` |
| `group:memory` | `memory_search`, `memory_get` |
| `group:web` | `web_search`, `web_fetch` |
| `group:ui` | `browser`, `canvas` |
| `group:automation` | `cron`, `gateway` |
| `group:messaging` | `message` |
| `group:nodes` | `nodes` |

---

## Core Tools

### `exec` - Shell Commands

```json
{
  "command": "npm test",
  "yieldMs": 10000,       // Auto-background after timeout
  "background": false,    // Immediate background
  "timeout": 1800,        // Seconds (kills process)
  "pty": false,           // TTY mode
  "elevated": false,      // Host execution if sandboxed
  "host": "sandbox",      // sandbox | gateway | node
  "security": "deny",     // deny | allowlist | full
  "node": "office-mac"    // Target node for host=node
}
```

### `process` - Background Sessions

Actions: `list`, `poll`, `log`, `write`, `kill`, `clear`, `remove`

```json
{ "action": "poll", "sessionId": "abc123", "timeout": 5000 }
{ "action": "log", "sessionId": "abc123", "offset": 0, "limit": 100 }
{ "action": "kill", "sessionId": "abc123" }
```

### `web_search` - Web Search

```json
{
  "query": "openclaw documentation",
  "count": 10,
  "country": "US",
  "language": "en",
  "freshness": "week"
}
```

Providers: Brave (default), Firecrawl, Gemini, Grok, Kimi, Perplexity

### `web_fetch` - URL Content

```json
{
  "url": "https://docs.openclaw.ai",
  "extractMode": "markdown",  // or "text"
  "maxChars": 50000
}
```

---

## Browser Tool

### Actions

| Action | Description |
|--------|-------------|
| `status` | Browser state |
| `start` | Launch browser |
| `stop` | Close browser |
| `tabs` | List tabs |
| `open` | Open URL |
| `snapshot` | Capture page structure (aria/ai) |
| `screenshot` | Capture image |
| `act` | UI interaction |
| `navigate` | Navigate to URL |
| `pdf` | Export PDF |

### Profiles

- `openclaw` (default): Isolated managed browser
- `user`: Real signed-in Chrome (requires user presence)
- Custom profiles: `work`, `remote`, etc.

```json
{ "action": "start", "profile": "openclaw" }
{ "action": "snapshot", "refs": "aria" }
{ "action": "act", "kind": "click", "ref": "e12" }
```

### Act Kinds

`click`, `type`, `press`, `hover`, `drag`, `select`, `fill`, `resize`, `wait`, `evaluate`

---

## Canvas Tool

Actions: `present`, `hide`, `navigate`, `eval`, `snapshot`, `a2ui_push`, `a2ui_reset`

```json
{ "action": "present", "url": "https://example.com" }
{ "action": "a2ui_push", "jsonl": "{\"type\":\"text\",\"text\":\"Hello\"}" }
{ "action": "snapshot" }
```

---

## Nodes Tool

### Actions

| Action | Description |
|--------|-------------|
| `status` | List connected nodes |
| `describe` | Node details |
| `notify` | Send notification |
| `run` | Execute command on node |
| `camera_snap` | Take photo |
| `camera_clip` | Record video |
| `screen_record` | Screen recording |
| `location_get` | Get GPS location |

```json
{ "action": "camera_snap", "node": "iphone-15", "facing": "back" }
{ "action": "run", "node": "mac-mini", "command": ["echo", "Hello"] }
```

---

## Messaging Tool

### Actions

| Action | Channels |
|--------|----------|
| `send` | All |
| `poll` | WhatsApp, Discord, MS Teams |
| `react` | Most |
| `edit`, `delete` | Most |
| `thread-create` | Discord, Slack, Telegram |
| `search` | Discord, Slack |

```json
{
  "action": "send",
  "channel": "telegram",
  "target": "-1001234567890",
  "message": "Hello!"
}
```

---

## Cron Tool

Actions: `status`, `list`, `add`, `update`, `remove`, `run`, `runs`, `wake`

```json
{
  "action": "add",
  "job": {
    "name": "Daily report",
    "schedule": { "kind": "cron", "expr": "0 9 * * *" },
    "payload": { "kind": "agentTurn", "message": "Generate report" },
    "sessionTarget": "isolated"
  }
}
```

---

## Sessions Tools

### `sessions_list`

```json
{ "limit": 20, "activeMinutes": 60, "messageLimit": 5 }
```

### `sessions_history`

```json
{ "sessionKey": "agent:main:main", "limit": 50, "includeTools": false }
```

### `sessions_send`

```json
{ "sessionKey": "agent:main:main", "message": "Hello", "timeoutSeconds": 60 }
```

### `sessions_spawn`

```json
{
  "task": "Build a hello world app",
  "runtime": "subagent",
  "mode": "run",
  "model": "zai/glm-5",
  "timeoutSeconds": 300
}
```

---

## Image Tool

```json
{ "image": "/path/to/image.png", "prompt": "Describe this image" }
```

---

## Gateway Tool

Actions: `restart`, `config.get`, `config.schema.lookup`, `config.apply`, `config.patch`, `update.run`

```json
{ "action": "restart", "delayMs": 2000 }
{ "action": "config.patch", "patch": { "agents": { "defaults": { "thinking": "high" } } } }
```

---

## Loop Detection

Prevent infinite tool-call loops:

```json5
{
  tools: {
    loopDetection: {
      enabled: true,
      warningThreshold: 10,
      criticalThreshold: 20,
      detectors: {
        genericRepeat: true,
        knownPollNoProgress: true,
        pingPong: true,
      },
    },
  },
}
```
