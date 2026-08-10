# 02 — Conversation Session Patterns (`@elevenlabs/client` v1.17.0)

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
```ts
const signedUrl = await fetch("/signed-url").then(r => r.text());

const conversation = await Conversation.startSession({
  signedUrl,
  connectionType: "websocket",
});
```

### Text-only session
```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  textOnly: true, // no mic permission, no audio stack
});
```

Returns a `TextConversation` (not `VoiceConversation`). Methods like `setVolume`, `setMicMuted`, and frequency data are no-ops or throw.

## Lifecycle callbacks to wire by default

Always implement:
- `onStatusChange` (connection state)
- `onError` (session failures — now defensively handled, won't crash on malformed payloads)
- `onDisconnect` (cleanup/retry)
- `onMessage` (transcript/assistant text stream)

For advanced observability:
- `onPing` — connection latency monitoring
- `onAudioAlignment` — char-level timing metadata (now works on WebRTC too)
- `onAgentReasoningResponsePart` — streaming reasoning (experimental)
- `onAgentChatResponsePart` — streaming chat response chunks
- `onInterruption`, `onVadScore`
- Tool-related callbacks (`onAgentToolRequest`, `onAgentToolResponse`, MCP hooks)

### `onConversationCreated` — early lifecycle hook

Fires **synchronously** during `startSession`, before `onConnect` and before status transitions to `"connected"`.

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  onConversationCreated: (conv) => {
    console.log("Created:", conv.getId());
  },
  onConnect: ({ conversationId }) => {
    console.log("Connected:", conversationId);
  },
});
```

**Ordering guarantee:** `onConversationCreated` → `markConnected` (status → `"connected"`) → `onConnect`.

### `onPing` — connection latency monitoring (v1.15.0)

The SDK automatically replies to server pings with `pong`. The callback is purely informational:

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  onPing: ({ ping_ms }) => {
    // ping_ms: estimated latency, may be null/undefined on first ping
    console.log(`Latency: ${ping_ms ?? "measuring"}ms`);
  },
});
```

### `onAgentReasoningResponsePart` — streaming reasoning (v1.16.0, experimental)

Receives `{ text, type, event_id }` where `type` is `"start"`, `"delta"`, or `"stop"`:

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  onAgentReasoningResponsePart: ({ text, type }) => {
    if (type === "start") console.log("Reasoning started");
    else if (type === "delta") process.stdout.write(text);
    else if (type === "stop") console.log("\nReasoning complete");
  },
});
```

> ⚠️ **Experimental** — may change without semver guarantee.

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

## Per-conversation ASR keyword biasing (v1.12.0)

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  overrides: {
    asr: {
      keywords: ["AcmeCorp", "Phalaenopsis", "Q3-earnings"],
    },
  },
});
```

Keywords are sent via `conversation_initiation_client_data` and bias the ASR model for this session only.

## Feedback: per-message targeting and clearing (v1.13.0 + v1.14.0)

```ts
// Rate the latest agent turn
conversation.sendFeedback(true);

// Rate a specific past message by event_id
conversation.sendFeedback(true, 42);

// Clear feedback on a specific message
conversation.sendFeedback(null, 42);
```

`canSendFeedback` now reflects **connection state** (not whether the latest turn is unrated), so you can re-rate any message while the session is live.

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  onCanSendFeedbackChange: ({ canSendFeedback }) => {
    setFeedbackEnabled(canSendFeedback);
  },
});
```

## Multimodal user turns

```ts
// Upload a file (image, PDF, etc.)
const { fileId } = await conversation.uploadFile(blob);

// Send with text
conversation.sendMultimodalMessage({
  text: "Summarize this PDF in 5 bullets",
  fileId,
});

// Or send file-only
conversation.sendMultimodalMessage({ fileId });
```

### React wrapper

```tsx
const { uploadFile, sendMultimodalMessage } = useConversationControls();

const handleUpload = async (file: File) => {
  const { fileId } = await uploadFile(file);
  sendMultimodalMessage({ fileId, text: "Describe this" });
};
```

### Types

```ts
type MultimodalMessageInput = { text?: string; fileId?: string };
type UploadFileResult = { fileId: string };
```

## Audio alignment metadata (v1.15.2 — WebRTC fix)

`onAudioAlignment` now fires correctly on both WebSocket and WebRTC transports. Previously, the WebRTC `DataReceived` handler dropped the entire `type: "audio"` JSON message (which carries alignment metadata alongside audio bytes). Now audio messages are routed through `handleMessage` so alignment is surfaced.

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  onAudioAlignment: ({ chars, char_start_times_ms, char_durations_ms }) => {
    // Use for lip-sync, karaoke-style highlighting, etc.
  },
});
```

## Dynamic variables

Dynamic variables are now replaced in the agent's **first message** (not just subsequent turns):

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  dynamicVariables: { customer_name: "Malik", order_id: "A-1234" },
});
```

## Tool mocking

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  toolMockConfig: {
    mockingStrategy: "selected",
    mockedToolNames: ["get_weather", "check_inventory"],
    fallbackStrategy: "call_real_tool",
  },
});
```

Options:
- `mockingStrategy`: `"none"` | `"all"` | `"selected"`
- `mockedToolNames`: tool names to mock when strategy is `"selected"`
- `fallbackStrategy`: `"raise_error"` | `"call_real_tool"` for unmocked tools

## Full tool result payloads (v0.13.0 types)

`onAgentToolResponse` can now receive full payload events alongside summary events:

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  onAgentToolResponse: (payload) => {
    if ("full_tool_result" in payload) {
      if (payload.truncated) console.warn("Full payload truncated to 64 KB");
      console.log(payload.tool_name, payload.full_tool_result);
    } else {
      console.log(payload.tool_call_id, payload.is_error);
    }
  },
});
```

Enable full payloads via the `agent_tool_response_full_payload` client event in agent configuration UI.

## Interruption handling (current behavior)

The interruption flow is **unconditional**:
1. `handleInterruption` always switches mode to `"listening"` and calls `output.interrupt()`, regardless of event ID ordering.
2. When new agent audio arrives, the client cancels pending fade-out, resets gain, clears pending interrupt timeout, and clears the `interrupted` flag before queuing the buffer.

## Do-not-miss production practices

- Request mic permission intentionally before starting session
- Keep API key server-side only
- Log conversation ID via `getId()` for incident correlation
- Implement explicit end path via `endSession()` to prevent dangling sessions
- Wire `onError` to surface session startup failures
- Use `onPing` for connection health monitoring dashboards
- Use `overrides.asr.keywords` for domain-specific terminology
- Use `enableLogging: false` (Scribe) for privacy-sensitive sessions (enterprise only)
