# 02 — Conversation Session Patterns (`@elevenlabs/client` v1.20.0)

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
- `onIncomingEvent` / `onOutgoingEvent` — raw socket event monitoring (main, unreleased)
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

## Connection event monitoring callbacks (main, unreleased)

`onIncomingEvent` and `onOutgoingEvent` fire for **every raw socket event** received from or sent to the server — before any SDK processing. They are monitoring/debug surfaces, not a stable API (typed as `any`).

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  onIncomingEvent: event => {
    // every server → client event, e.g. { type: "audio", ... }
  },
  onOutgoingEvent: event => {
    // every client → server event, e.g. { type: "user_audio_chunk", ... }
  },
});
```

Behavior details:
- `onIncomingEvent` fires at the top of the message dispatcher, before the switch on event type
- Outgoing events are queued until the callback is attached (constructor-time attachment flushes via microtask), so nothing sent during setup is lost
- Both are exposed in `@elevenlabs/react` `HookCallbacks` (unreleased) and listed in `CALLBACK_KEYS`
- Do not use them to build features — they may change without notice; prefer the typed callbacks

## WebRTC ICE transport policy (v1.20.0)

Set `webRtc.iceTransportPolicy` in the session config for WebRTC connections. Use `"relay"` to restrict ICE candidates to TURN relay — the fix for corporate/mobile networks that drop direct UDP flows:

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  connectionType: "webrtc",
  webRtc: {
    iceTransportPolicy: "relay", // TURN-only; default is "all"
  },
});
```

- Values: `"all"` (default) | `"relay"`
- Only affects WebRTC connections; WebSocket sessions ignore it
- Combine with your agent's TURN configuration — the policy only filters candidates the server/peer already offers

## Self-hosted AudioWorklets under strict CSP (WebRTC fixed in v1.20.0)

`workletPaths` now applies on the **WebRTC connection path** too (previously only WebSocket), so self-hosted AudioWorklet files are used for output capture instead of falling back to `blob:`/`data:` URLs under a strict CSP. The worklet module cache is also keyed by the requested source, so a self-hosted `workletPaths` entry is no longer served a stale inlined `blob:` URL cached by an earlier default load.

If you self-host worklets and previously saw CSP violations on WebRTC sessions, re-test — this was the likely cause.

## React Native setup guard (v1.20.0)

On React Native, when no voice session setup strategy is registered, the error message now points at importing `@elevenlabs/react-native` instead of suggesting the browser entry point. If you see this error, add:

```ts
import "@elevenlabs/react-native"; // registers the RN voice session setup strategy
```

## Self-hosted orchestrator sessions (v1.18.0, experimental)

Route conversations to a self-hosted orchestrator (private deployment) instead of the ElevenLabs cloud. The orchestrator config is sent as an `enclave_setup_config` event on the WebSocket immediately after connection.

```ts
import { Conversation } from "@elevenlabs/client";

const conversation = await Conversation.startSession({
  orchestrator: {
    url: "wss://<your-host>/sagemaker/convai/conversation",
    agentConfig: { /* full agent definition from platform export */ },
    agentConfigOverrides: [ /* partial override objects */ ],
    tools: [ /* tool definitions in platform export format */ ],
    promptKnowledgeBase: ["Context line 1", "Context line 2"],
    bedrockInferenceProfile: "global", // default: "us"
    postCallTranscriptionWebhook: {
      url: "https://your-app/post-transcript",
      hmacSecret: "super-secret-at-least-16-chars",
    },
    postCallAudioWebhook: {
      url: "https://your-app/post-audio",
    },
  },
});
```

### Constraints
- **WebSocket only** — setting `connectionType: "webrtc"` throws
- Cannot combine with `signedUrl`, `conversationToken`, `authorization`, `origin`, or `environment`
- `uploadFile()` throws — files would leave the customer network
- The `hmacSecret` is visible to the end user (client-side); intended for testing against trusted deployments, not production

### Post-call webhooks
The orchestrator can deliver transcripts and/or audio to your endpoints after the session ends:
- `postCallTranscriptionWebhook`: receives the conversation transcript
- `postCallAudioWebhook`: receives the conversation audio
- HMAC secrets must be ≥ 16 characters; the orchestrator signs deliveries with them

### Exported types
```ts
import type {
  OrchestratorConfig,
  OrchestratorSessionConfig,
  PostCallWebhookConfig,
} from "@elevenlabs/client";
```

### Widget: self-hosted orchestrator attributes (widget-core 0.16.0, experimental)

The embedded widget can connect to a self-hosted orchestrator without any JS, via two new HTML attributes:

```html
<elevenlabs-convai
  agent-id="agent_xxx"
  orchestrator-url="https://your-host/sagemaker/convai/conversation"
  orchestrator-agent-config='{"agent_config_dict": { ... }, "tools_config_list": [ ... ], "post_call_transcription_webhook": {"url": "https://your-app/post-transcript", "hmac_secret": "at-least-16-chars"}}'
></elevenlabs-convai>
```

Rules (from `parseOrchestratorConfig`):
- `orchestrator-url` **takes precedence** — `agent-id` and `signed-url` are ignored (console warning if both are set). No HTTP config fetch happens; the widget connects straight to your orchestrator.
- `http(s)://` URLs are auto-converted to `ws(s)://`.
- `orchestrator-agent-config` (optional) must be a JSON **object** with snake_case keys mapped to `OrchestratorConfig`:
  - `agent_config_dict` / `agent_config` → `agentConfig`
  - `override_agent_config_list` / `override_agent_config` → `agentConfigOverrides`
  - `tools_config_list` / `tools_config` → `tools`
  - `prompt_knowledge_base` → `promptKnowledgeBase` (string array)
  - `bedrock_inference_profile` → `bedrockInferenceProfile` (string)
  - `post_call_transcription_webhook` / `post_call_audio_webhook` → `{ url, hmac_secret? }` — `url` must be a non-empty string; if `hmac_secret` is present it must be a string, otherwise the whole config is rejected
- Invalid JSON, non-object root, or bad webhooks → `console.error("[ConversationalAI] ...")` and orchestrator config is dropped (null) — the widget will not connect with a partially-parsed config

## Rich content callback (v1.18.0, experimental)

The `onRichContent` callback fires when the agent sends a component for the client to display (e.g., an item card). The callback receives `{ rich_content_id, component, props, event_id }`. Nothing is sent back — the agent's turn does not wait on the client.

> ⚠️ **Experimental.** The server currently only offers components to the embedded widget. The callback will not fire for other consumers. Treat `props` as agent-authored and untrusted when rendering into a document.

```ts
const conversation = await Conversation.startSession({
  agentId: "agent_xxx",
  onRichContent: ({ rich_content_id, component, props, event_id }) => {
    console.log(`Component: ${component}`, props);
    // Render the component based on `component` string and `props` object
  },
});
```

### Widget rich content rendering (widget-core 0.16.0)

The embedded widget now renders rich content components inline in the transcript (experimental). Current supported component: **`buttons`** (quick-reply button group).

- **`message` button** — `{ type: "message", label, message }`: sends `message` as the user's own turn via `sendUserMessage` (disabled unless connected)
- **`link` button** — `{ type: "link", label, link }`: opens `link` in a new tab; `link` must start with `https://` (max 2048 chars)

Validation before render (zod-mini, ~5 KB gzipped added):
- Max **3 buttons** per group (extras silently dropped); at least 1 valid button required
- Labels/messages trimmed, 1–500 chars (overlong text truncated)
- Malformed buttons dropped; malformed/unrecognized components render a small "This content could not be displayed" notice instead of breaking the transcript
- Rich content stays where it arrived in the transcript — no messages are moved across it

Example of what the agent sends: `component: "buttons"`, `props: { buttons: [{ type: "message", label: "Book it", message: "Book the 3pm slot" }, { type: "link", label: "Details", link: "https://example.com/details" }] }`

## Disconnect state consistency (v1.18.0 fix, react 1.12.1)

Several fixes ensure robust session lifecycle:

1. **`BaseConversation.endSessionWithDetails`** now always reaches `"disconnected"` status and fires `onDisconnect`, even if session teardown throws.
2. **`ConversationProvider`** no longer lets a late `onDisconnect` from a previous session clear a newer session's state.
3. **`useConversationStatus`** now reports `"disconnected"` as soon as teardown starts (status → `"disconnecting"`), instead of holding `"connected"` while the conversation was already released.
4. **`endSession()` rejections** are caught and warned rather than left unhandled.

Practical impact: consumers guarding conversation access with `status === "connected"` no longer crash during disconnect, and rapid session restarts (superseding a previous session) are safe.

## Do-not-miss production practices

- Request mic permission intentionally before starting session
- Keep API key server-side only
- Log conversation ID via `getId()` for incident correlation
- Implement explicit end path via `endSession()` to prevent dangling sessions
- Wire `onError` to surface session startup failures
- Use `onPing` for connection health monitoring dashboards
- Use `overrides.asr.keywords` for domain-specific terminology
- Use `enableLogging: false` (Scribe) for privacy-sensitive sessions (enterprise only)
