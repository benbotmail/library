# 02 — Conversation Session Patterns (`@elevenlabs/client`)

## Canonical start patterns

### Public agent (quickest)
```ts
import { Conversation } from "@elevenlabs/client";

const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  connectionType: "webrtc", // or "websocket"
});
```

### Private agent (recommended for production)
1) Backend mints signed URL/token using `xi-api-key`
2) Frontend starts session with returned credential

```ts
const signedUrl = await fetch("/signed-url").then(r => r.text());

const conversation = await Conversation.startSession({
  signedUrl,
  connectionType: "websocket",
});
```

## Lifecycle callbacks to wire by default

Always implement:
- `onStatusChange` (connection state)
- `onError` (session failures)
- `onDisconnect` (cleanup/retry)
- `onMessage` (transcript/assistant text stream)

For advanced observability:
- `onDebug`
- `onAudio`
- `onInterruption`
- `onVadScore`
- tool-related callbacks (`onAgentToolRequest`, `onAgentToolResponse`, MCP hooks)

## Practical reliability preset

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  connectionType: "webrtc",
  connectionDelay: { android: 3000, ios: 0, default: 0 },
  preferHeadphonesForIosDevices: true,
  useWakeLock: true,
  onStatusChange: s => console.log("status", s),
  onError: e => console.error(e),
});
```

Why this matters:
- Android delay mitigates first-utterance clipping
- Wake lock reduces session drop from screen sleep
- Explicit status/error hooks prevent silent failure

## Agent selection UX pattern (current upstream example)

The current `examples/agent-testbench` index flow uses a **dropdown selector** for agents (rather than rendering a full horizontal button list). This scales better when agent counts grow.

```tsx
<DropdownMenu>
  <DropdownMenuTrigger asChild>
    <Button variant="outline">Select Agent</Button>
  </DropdownMenuTrigger>
  <DropdownMenuContent>
    {agents.map(agent => (
      <DropdownMenuItem key={agent.agentId} asChild>
        <Link to={`/agents/$agentId`} params={{ agentId: agent.agentId }}>
          {agent.name}
        </Link>
      </DropdownMenuItem>
    ))}
  </DropdownMenuContent>
</DropdownMenu>
```

Use this in operator/testbench UIs where account users may have many configured agents.

## Text-first or hybrid modes

- Full voice+text: default session
- Text-only: pass `textOnly: true` (no mic prompt path)

Use text-only for moderation/testing harnesses when you want model behavior without audio stack variability.

## Do-not-miss production practices

- Request mic permission intentionally before starting session
- Keep API key server-side only
- Log conversation ID via `getId()` for incident correlation
- Implement explicit end path via `endSession()` to prevent dangling sessions