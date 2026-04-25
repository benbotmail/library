# Built-in Tools Reference

Core tools available to agents for common tasks.

Run `openclaw tools list` to see available tools in your configuration.

## Tool availability

Tool availability depends on:
- Gateway configuration (`tools.defaults.*`)
- Channel-specific overrides (`tools.channels.*`)
- Session permissions
- Agent model support (some tools require vision, etc.)

## Core tools

### exec
Execute shell commands on the host system.

**Permissions:**
- Requires `exec.enabled: true` in config
- Elevated commands require user approval

**Parameters:**
- `command` (string, required): Shell command to execute
- `workdir` (string, optional): Working directory
- `timeout` (number, optional): Timeout in seconds
- `pty` (boolean, optional): Run in pseudo-terminal (for TTY-required CLIs)

**Examples:**

Simple command:
```bash
ls -la /home/user/
```

With timeout:
```json
{
  "command": "npm install",
  "timeout": 300
}
```

With PTY for interactive tools:
```json
{
  "command": "vim",
  "pty": true
}
```

**Use cases:**
- File operations (read, write, delete)
- Package management (apt, npm, brew)
- Build and deploy tasks
- System administration

**See:** `52_EXEC_SECURITY_AND_APPROVALS.md`

### browser
Control web browsers for automation, scraping, and testing.

**Parameters:**
- `action` (string, required): Action to perform
  - `start` — Launch browser
  - `stop` — Close browser
  - `open` — Navigate to URL
  - `snapshot` — Capture page state
  - `screenshot` — Take screenshot
  - `act` — Perform action (click, type, wait, etc.)
  - `navigate` — Navigate to URL
- `url` (string, optional): URL to navigate to
- `targetId` (string, optional): Target tab ID
- `ref` (string, optional): Element reference (for act)
- `type` (string, optional): Action type (click, type, press, wait, etc.)
- `text` (string, optional): Text to type
- `selector` (string, optional): CSS selector

**Profiles:**
- Default (default) — Isolated OpenClaw-managed browser
- `user` — Logged-in user browser (local host)
- `chrome-relay` — Chrome extension / Browser Relay

**Examples:**

Navigate and snapshot:
```json
{
  "action": "open",
  "url": "https://example.com"
}
```

Click element:
```json
{
  "action": "act",
  "type": "click",
  "ref": "aria-button[name=Submit]"
}
```

Type in input:
```json
{
  "action": "act",
  "type": "type",
  "inputRef": "aria-textbox[name=Username]",
  "text": "myusername"
}
```

**Use cases:**
- Web scraping and data extraction
- Form filling and submission
- Testing web applications
- Automating browser workflows

**See:** `53_BROWSER_CONTROL.md`

### web_search
Search the web using Brave Search API.

**Permissions:**
- Requires `tools.defaults.webSearch.enabled: true`
- Requires Brave API key: `openclaw secret set brave.search.apiKey YOUR_KEY`

**Parameters:**
- `query` (string, required): Search query
- `count` (number, optional): Number of results (1-10)
- `freshness` (string, optional): Time filter (day, week, month, year)
- `language` (string, optional): Language code (en, de, fr, etc.)

**Examples:**

Basic search:
```json
{
  "query": "OpenClaw documentation"
}
```

Recent results:
```json
{
  "query": "OpenClaw news",
  "freshness": "week",
  "count": 5
}
```

**Use cases:**
- Information retrieval
- Current events and news
- Research and fact-checking
- Finding documentation and examples

### message
Send messages to channels or perform channel actions.

**Actions:**
- `send` — Send message to channel
- `broadcast` — Send to multiple channels
- `poll` — Create or vote on polls
- `react` — Add emoji reaction
- `delete` — Delete message
- `edit` — Edit message
- `topic-create` — Create thread/topic

**Parameters (send):**
- `channel` (string, required): Channel type (telegram, discord, etc.)
- `to` (string, required): Target channel/user ID or name
- `message` (string, required): Message content

**Examples:**

Send message:
```json
{
  "action": "send",
  "channel": "telegram",
  "to": "123456789",
  "message": "Hello from OpenClaw!"
}
```

React to message:
```json
{
  "action": "react",
  "emoji": "👍",
  "messageId": "123"
}
```

Create poll:
```json
{
  "action": "poll",
  "channel": "discord",
  "pollQuestion": "Favorite framework?",
  "pollOption": ["React", "Vue", "Angular", "Svelte"]
}
```

**Use cases:**
- Proactive notifications
- Broadcasting alerts
- Polling and feedback collection
- Message management

### sessions_spawn
Spawn isolated sub-agent sessions for parallel or specialized tasks.

**Runtimes:**
- `subagent` — OpenClaw sub-agent
- `acp` — ACP harness (Codex, Claude Code, Pi)

**Parameters:**
- `task` (string, required): Task description
- `runtime` (string, optional): subagent or acp (default: subagent)
- `agentId` (string, optional): Specific agent to use
- `mode` (string, optional): run (one-shot) or session (persistent)
- `thread` (boolean, optional): Create Discord thread (for ACP)
- `cleanup` (string, optional): delete or keep (for run mode)
- `cwd` (string, optional): Working directory
- `timeoutSeconds` (number, optional): Execution timeout

**Examples:**

One-shot sub-agent:
```json
{
  "task": "Summarize this file",
  "runtime": "subagent",
  "mode": "run",
  "cleanup": "delete"
}
```

Persistent ACP session:
```json
{
  "task": "Fix this bug",
  "runtime": "acp",
  "agentId": "codex",
  "mode": "session",
  "thread": true
}
```

**Use cases:**
- Coding tasks (via coding-agent skill)
- Parallel processing
- Isolated environments
- Specialized agent workflows

**See:** `40_AGENT_RUNTIME.md`, `43_MULTI_AGENT_ROUTING.md`

### cron
Schedule tasks and reminders.

**Parameters:**
- `action` (string, required): create, list, delete, or enable/disable
- `name` (string, required): Job name
- `schedule` (object, optional): Schedule definition
  - `cron` (string): Cron expression
  - `every` (string): Interval (e.g., "30m", "2h", "1d")
  - `at` (string): Absolute time (ISO 8601)
- `payload` (object, optional): Job payload
  - `type`: systemEvent or agentTurn
  - `text`: Message or command

**Examples:**

Create daily job:
```json
{
  "action": "create",
  "name": "morning-check",
  "schedule": {
    "cron": "0 9 * * *"
  },
  "payload": {
    "type": "systemEvent",
    "text": "Good morning!"
  }
}
```

Create reminder:
```json
{
  "action": "create",
  "name": "meeting-reminder",
  "schedule": {
    "at": "2026-03-18T14:00:00Z"
  },
  "payload": {
    "type": "agentTurn",
    "message": "Meeting in 15 minutes"
  }
}
```

**Use cases:**
- Scheduled reports
- Reminders and alerts
- Periodic maintenance tasks
- Heartbeat monitoring

**See:** `70_CRON_AND_SCHEDULING.md`

### image
Analyze images with vision models.

**Parameters:**
- `prompt` (string, required): What to analyze
- `image` (string, optional): Single image path or URL
- `images` (array, optional): Multiple images (up to 20)
- `model` (string, optional): Model to use

**Examples:**

Analyze single image:
```json
{
  "prompt": "Describe this screenshot",
  "image": "/path/to/screenshot.png"
}
```

Analyze multiple images:
```json
{
  "prompt": "Compare these two images",
  "images": ["/path/to/image1.png", "/path/to/image2.png"]
}
```

**Use cases:**
- Screenshot analysis
- Image recognition
- Visual question answering
- Document processing

### tts
Convert text to speech and deliver to channel.

**Parameters:**
- `text` (string, required): Text to speak
- `channel` (string, optional): Channel for format selection

**Use cases:**
- Voice messages
- Accessibility
- Audio responses

### memory_search
Search semantic memory across MEMORY.md and memory/ files.

**Parameters:**
- `query` (string, required): Search query
- `maxResults` (number, optional): Max results to return
- `minScore` (number, optional): Minimum similarity score

**Use cases:**
- Retrieving prior decisions
- Checking memory for context
- Finding notes and documentation

**See:** `44_MEMORY_AND_CONTEXT.md`

### memory_get
Read specific memory file snippets.

**Parameters:**
- `path` (string, required): File path
- `from` (number, optional): Line number to start
- `lines` (number, optional): Number of lines to read

**Use cases:**
- Retrieving specific memory sections
- Reading long files efficiently

## File operations tools

### read
Read file contents.

**Parameters:**
- `path` (string, required): File path
- `offset` (number, optional): Line number to start from
- `limit` (number, optional): Max lines to read

### write
Write or overwrite files.

**Parameters:**
- `path` (string, required): File path
- `content` (string, required): Content to write

### edit
Make precise edits to files.

**Parameters:**
- `path` (string, required): File path
- `oldText` (string, required): Text to find
- `newText` (string, required): Replacement text

## Session management tools

### sessions_list
List sessions with filters.

**Parameters:**
- `activeMinutes` (number, optional): Filter by recent activity
- `kinds` (array, optional): Session kinds to filter
- `limit` (number, optional): Max results

### sessions_history
Fetch message history for a session.

**Parameters:**
- `sessionKey` (string, required): Session identifier
- `limit` (number, optional): Max messages
- `includeTools` (boolean, optional): Include tool calls

### sessions_send
Send message to another session.

**Parameters:**
- `sessionKey` (string, optional): Session key
- `label` (string, optional): Session label
- `message` (string, required): Message to send

## Agent management tools

### subagents
List, steer, or kill sub-agents.

**Parameters:**
- `action` (string, required): list, kill, or steer
- `target` (string, optional): Sub-agent ID
- `message` (string, optional): Message to send (for steer)

## Canvas tools

### canvas
Control node canvases for remote UI.

**Parameters:**
- `action` (string, required): present, hide, navigate, eval, or snapshot
- `url` (string, optional): URL to navigate
- `javaScript` (string, optional): JS to evaluate
- `outputFormat` (string, optional): png or jpg

**See:** `54_NODE_PAIRING_AND_CANVAS.md`

## Tool permission model

**Permission levels:**
1. **Always allowed** — Runs without approval
2. **Elevated** — Requires user approval before execution
3. **Disabled** — Not available to agents

**Configuration:**
```json5
{
  tools: {
    defaults: {
      exec: { elevated: true },
      browser: { enabled: true },
    },
    channels: {
      discord: {
        exec: { elevated: true },
        sessions_spawn: { enabled: false },
      },
    },
  },
}
```

## Tool timeouts

Most tools support timeouts to prevent hanging:

```json
{
  "command": "npm install",
  "timeout": 300  // 5 minutes
}
```

Default timeouts:
- `exec`: 60 seconds (configurable)
- `browser`: 30 seconds per action
- `web_search`: 10 seconds
- File operations: No timeout (blocking I/O)

## Tool errors

Common error patterns:

**Permission denied:**
```
Error: Tool not enabled in configuration
```
**Fix:** Enable tool in `openclaw.json` or per-channel overrides

**Approval required:**
```
Error: Elevated command requires approval
```
**Fix:** Run `/approve allow-once` or `/approve allow-always`

**Timeout:**
```
Error: Command timed out after 60s
```
**Fix:** Increase timeout or optimize command

**Execution failed:**
```
Error: Command exited with code 1
```
**Fix:** Check command syntax, dependencies, and logs

## Provenance
- **Source:** AGENTS.md, docs/tools/, `src/`
- **Last validated:** 2026-03-18 (against openclaw@latest from GitHub)
