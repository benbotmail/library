# OpenClaw Tools Reference

> Current as of 2026-08-19 (upstream `7a82d8b0f25`).

## Tool Control

### Allow/Deny Lists

```json5
{
  tools: {
    profile: "full",            // minimal | coding | messaging | full (default)
    allow: ["group:fs", "browser"],
    deny: ["exec"],             // deny wins over allow
    elevated: [],               // tools that bypass sandbox
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

### Tool Policy Layers (Intersecting)

A deny in **any** layer blocks the tool:

1. Global `tools.deny` / `tools.allow`
2. Per-agent `agents.entries.*.tools`
3. Per-channel `channels.<channel>.direct.<chatId>.tools`
4. Per-sender `toolsBySender`
5. Sandbox tool policy (`tools.sandbox.tools`)

### `toolsBySender`

```json5
{
  channels: {
    telegram: {
      direct: {
        "*": { tools: { deny: ["write", "edit"] } },
        "603767951": { tools: {} },  // this sender gets full tools
      },
    },
  },
}
```

A matching `toolsBySender` entry replaces `tools` for that DM. An exact chat entry replaces the `"*"` entry (no inheritance).

---

## Core Tools

### `exec` — Shell Commands

```json
{
  "command": "npm test",
  "yieldMs": 10000,
  "background": false,
  "timeout": 1800,
  "pty": false,
  "elevated": false,
  "host": "sandbox",
  "security": "deny",
  "node": "office-mac"
}
```

**Security modes:** `deny` (default) | `allowlist` | `full`
**Elevated commands** require `/approve` from operator (allow-once; fresh approval per command).

### `process` — Background Sessions

Actions: `list`, `poll`, `log`, `write`, `send-keys`, `submit`, `paste`, `kill`, `clear`, `remove`

### `web_search` — Web Search

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

### `web_fetch` — URL Content

```json
{
  "url": "https://docs.openclaw.ai",
  "extractMode": "markdown",
  "maxChars": 50000
}
```

---

## Browser Tool

| Action | Description |
|--------|-------------|
| `status` | Browser state |
| `start` | Launch browser |
| `stop` | Close browser |
| `profiles` | List profiles |
| `tabs` | List tabs |
| `open` | Open URL |
| `snapshot` | Capture page structure (aria/role refs) |
| `screenshot` | Capture image |
| `act` | UI interaction (click, type, press, hover, drag, select, fill, etc.) |
| `navigate` | Navigate to URL |
| `pdf` | Export PDF |
| `console` | Console output |
| `upload` | File upload |
| `dialog` | Handle dialogs |

### Profiles
- `openclaw` (default): Isolated managed browser
- `user`: Real signed-in Chrome (requires user presence)
- Custom profiles: `work`, `remote`, etc.

### Refs
- `refs="aria"`: Playwright aria-ref ids (stable across calls)
- `refs="role"` (default): role+name-based refs
- Keep `targetId` from snapshot in subsequent calls

---

## Canvas Tool

Actions: `present`, `hide`, `navigate`, `eval`, `snapshot`, `a2ui_push`, `a2ui_reset`

---

## Nodes Tool

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

---

## Messaging Tool

| Action | Description |
|--------|-------------|
| `send` | Send message to channel |
| `broadcast` | Broadcast to multiple targets |
| `poll` | Poll for messages (WhatsApp, Discord, MS Teams) |
| `react` | React with emoji |
| `edit` | Edit message |
| `delete` | Delete message |
| `topic-create` | Create topic/thread (Discord, Slack, Telegram) |
| `topic-edit` | Edit topic |

---

## Cron Tool

Actions: `status`, `list`, `add`, `update`, `remove`, `run`, `runs`, `wake`, `scratch`

Note: `cron.run` accepts `mode: "if-enabled"` (run immediately without overriding a disabled job — for direct Gateway event sources) vs `mode: "force"`/`now` (operator run-now). A `sessionKey` target requires `mode: "now"`, `hooks.allowRequestSessionKey: true`, and must match `hooks.allowedSessionKeyPrefixes` when configured.

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

Use `runtime: "acp"` for ACP harness sessions (Codex, Claude Code, Pi). Set `thread: true` for Discord thread-bound persistent sessions.

---

## Image Tool

```json
{ "image": "/path/to/image.png", "prompt": "Describe this image" }
{ "images": ["url1", "url2"], "prompt": "Compare these" }
```

---

## PDF Tool

```json
{ "pdf": "/path/to/doc.pdf", "prompt": "Summarize this" }
{ "pdfs": ["url1", "url2"], "pages": "1-5" }
```

---

## Gateway Tool

Actions: `restart`, `config.get`, `config.schema.lookup`, `config.apply`, `config.patch`, `update.run`

---

## TTS Tool

Converts text to speech. Audio delivered automatically from tool result.

---

## Loop Detection

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
