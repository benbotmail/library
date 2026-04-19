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

### `onConversationCreated` — new lifecycle hook (v1.2.1)

Fires **synchronously** during `startSession`, before `onConnect` and before the status transitions to `"connected"`. Use it to capture the `Conversation` instance early — before any async gap could let a competing `endSession` or stale start race past you.

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  onConversationCreated: (conv) => {
    // conv is available immediately, before onConnect fires
    console.log("Created:", conv.getId());
  },
  onConnect: ({ conversationId }) => {
    console.log("Connected:", conversationId);
  },
});
```

**Ordering guarantee (v1.2.1):** `onConversationCreated` → `markConnected` (status → `"connected"`) → `onConnect`.

**Why this matters in React:** The `ConversationProvider` now sets `conversationRef` inside `onConversationCreated` instead of waiting for the `startSession` promise to resolve. This eliminates the race where `onConnect` fired before the ref was populated.

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

## Multimodal user turns (new canonical path)

Current client surface supports sending a user turn with optional text plus optional file reference in one event (`type: "multimodal_message"`).

```ts
conversation.sendMultimodalMessage({
  text: "Summarize this PDF in 5 bullets",
  fileId: "file_abc123",
});
```

Notes:
- `text` and `fileId` are both optional; include at least one in practice.
- Event contract maps to `{ type: "multimodal_message", text?: { type: "user_message", text }, file?: { type: "file_input", file_id } }`.
- React wrapper now exposes the same method through `useConversation(...).sendMultimodalMessage(...)`.

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

## Volume metering (v1.2.0+)

The client now exposes `getVolume()` and `getByteFrequencyData()` on both `InputController` and `OutputController`. The old `getAnalyser()` is deprecated.

```ts
// Current volume level (0–1 float)
const inputVol = await conversation.getInputVolume();
const outputVol = await conversation.getOutputVolume();

// Frequency data focused on human voice range (100–8000 Hz)
const freqData = new Uint8Array(256);
await conversation.input.getByteFrequencyData(freqData);
```

**Breaking (v1.2.0):** `getByteFrequencyData()` returns data focused on the human voice range (100–8000 Hz) instead of full spectrum. If you need full-spectrum data, use the deprecated `getAnalyser()` to access the raw `AnalyserNode` directly.

**React Native:** Volume metering now works via native LiveKit RMS/FFT processors — no more returning 0. The SDK auto-wires `setInputVolumeProvider`/`setOutputVolumeProvider` through `@livekit/react-native`.

## Device switching (simplified in v1.2.0)

`format` and `sampleRate` are now optional in `changeInputDevice`/`changeOutputDevice`:

```ts
// Before: had to specify format + sampleRate every time
await conversation.changeInputDevice({ deviceId: "new-mic", format: "pcm", sampleRate: 16000 });

// Now: just pass the device ID
await conversation.changeInputDevice({ deviceId: "new-mic" });
```

## Error surfacing in React (v1.1.0)

`ConversationProvider` now correctly propagates `startSession` rejections (e.g. "agent not found") through `onError`. Previously, the UI would silently get stuck in "connecting". No code changes needed — just make sure you have an `onError` handler wired.

## Do-not-miss production practices

- Request mic permission intentionally before starting session
- Keep API key server-side only
- Log conversation ID via `getId()` for incident correlation
- Implement explicit end path via `endSession()` to prevent dangling sessions
- Wire `onError` to surface session startup failures to the user (no longer swallowed)
