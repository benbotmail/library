# 01 — Product Surface and Scope (Current)

## What is actively relevant

### 1) `@elevenlabs/client` v1.18.0 (primary)
Use this for:
- Conversational agent sessions — voice (`VoiceConversation`) and text-only (`TextConversation`) via `Conversation.startSession`
- Realtime speech-to-text with Scribe (`Scribe.connect`)
- Event-driven session control and audio/device management
- Multimodal turn submission via `sendMultimodalMessage({ text?, fileId? })` + `uploadFile()`
- Per-conversation ASR keyword biasing via `overrides.asr.keywords`
- Text-only mode via `textOnly: true` option
- Self-hosted orchestrator sessions via `orchestrator` config (experimental)
- Unity WebGL bridge via `@elevenlabs/client/internal/unity`
- `sendFeedback` with per-message targeting (`eventId`) and null-clearing
- Platform abstraction: runtime compatibility checks, `WebRTCAudioAdapter`
- Full streaming reasoning callbacks (`onAgentReasoningResponsePart` — experimental)
- Rich content callbacks (`onRichContent` — experimental): agent-sent UI components
- Connection latency monitoring via `onPing`
- Raw connection monitoring via `onIncomingEvent` / `onOutgoingEvent` (main, unreleased)
- Audio alignment metadata via `onAudioAlignment` (works on WebRTC transport)
- Scribe `workletPaths.scribeAudioProcessor` for self-hosting worklets under strict CSP

### 2) Framework + integration packages
- `@elevenlabs/react` v1.12.1 — `ConversationProvider`, `useConversation`, `useScribe` hooks; full callback parity with client
- `@elevenlabs/react-native` v1.2.19 — modular imports, no provider pattern
- `@elevenlabs/types` v0.20.0 — generated types only; manual types now live in `@elevenlabs/client`
- `@elevenlabs/convai-widget-core` v0.16.0 — file upload, `EventBridge`, audio tags, dynamic variables, `show_resize_button`, self-hosted orchestrator attributes, rich content rendering
- `@elevenlabs/convai-widget-embed` v0.16.0 — keep in sync with core

### 3) Key architecture changes since earlier v1.x
- **Types relocation**: `Role`, `Mode`, `Status`, `Callbacks`, `CALLBACK_KEYS`, `DisconnectionDetails`, `MessagePayload`, `AudioAlignmentEvent` moved from `@elevenlabs/types` to `@elevenlabs/client` — import from client now
- **Platform layer**: Browser-specific code reorganized under `packages/client/src/platform/web/` (input, output, audio worklets, microphone, compatibility, volume provider)
- **Runtime checks**: New `runtime.ts` asserts runtime compatibility before session start
- **Scribe microphone**: Extracted to `scribe/microphone.ts` with proper teardown on failure
- **Widget file upload**: `useFileUpload` hook, `UploadFileButton`, `PendingFilePreview` components

---

## Connection/auth model

### Connection/auth model

### Conversation sessions
Four mutually exclusive auth/connection modes:
- **Public agent:** client starts with `agentId`
- **Private agent (WebSocket):** your server mints a signed URL
- **Private agent (WebRTC):** your server mints a conversation token
- **Self-hosted orchestrator (experimental):** client provides `orchestrator` config pointing to a private deployment WebSocket URL; agent config is sent at connection time

The `ConnectionFactory.determineConnectionType` enforces these exclusions:
- Orchestrator sessions throw if `connectionType` is set to `"webrtc"`
- Orchestrator sessions throw if combined with `signedUrl`, `conversationToken`, or `authorization`
- Orchestrator sessions always use WebSocket transport

### Scribe realtime STT
- Client uses **single-use token** from server endpoint
- Server calls ElevenLabs token endpoint using secret API key
- Client never receives your permanent API key
- `enableLogging: false` activates zero retention mode (enterprise only)

---

## Current constraints that matter in architecture

- WebRTC conversation mode uses fixed `pcm_48000` for input/output audio path
- Browser/device sample-rate mismatch handled by loading libsamplerate worklet when needed
- Scribe `previousText` is only valid in the **first** audio chunk of a session
- For conference subtitles, treat partials as provisional and committed transcripts as authoritative
- `onAgentReasoningResponsePart` is **experimental** — may change without semver guarantee
- `onRichContent` is **experimental** — agent-sent UI components, may change without semver guarantee; `props` is agent-authored and untrusted
- Self-hosted orchestrator sessions are **experimental** — websocket-only, cannot combine with `signedUrl`, `conversationToken`, or `authorization`
- Orchestrator sessions do not support `uploadFile()` — the file body would leave the customer network
- `sendFeedback` `canSendFeedback` now reflects connection state (not latest-turn-rated state), allowing re-rating of any message while session is live
- `overrides.asr.keywords` biases ASR per-conversation via `conversation_initiation_client_data`
- Widget `show_resize_button` defaults to `true`; set `false` to hide expand/collapse control
- Dynamic variables are now replaced in the agent's **first message** (not just subsequent turns)
- Widget text streaming is re-enabled in voice sessions (was temporarily disabled, now restored)
- Widget voice transcripts now render markdown correctly (fixed in latest patch)

---

## Callback surface (complete current reference)

All callbacks available on `Conversation.startSession()` options and React `<ConversationProvider>`:

| Callback | Purpose | Since |
|---|---|---|
| `onConnect` | Session connected, conversation ID available | v1.0 |
| `onDisconnect` | Session ended with `DisconnectionDetails` | v1.0 |
| `onError` | Session failures (defensively handled) | v1.0 |
| `onMessage` | User/agent text messages | v1.0 |
| `onAudio` | Raw base64 audio chunks | v1.0 |
| `onModeChange` | Speaking/listening mode switch | v1.0 |
| `onStatusChange` | Connection status transitions | v1.0 |
| `onCanSendFeedbackChange` | Feedback availability changed | v1.0 |
| `onVadScore` | Voice activity detection scores | v1.0 |
| `onUnhandledClientToolCall` | Client-side tool not handled | v1.0 |
| `onAgentToolRequest` | Agent requests a tool | v1.0 |
| `onAgentToolResponse` | Tool result (summary or full payload) | v1.0 |
| `onMCPToolCall` | MCP tool invocation | v1.0 |
| `onMCPConnectionStatus` | MCP connection state | v1.0 |
| `onConversationMetadata` | Session metadata event | v1.0 |
| `onAsrInitiationMetadata` | ASR metadata | v1.0 |
| `onInterruption` | User interrupted agent | v1.0 |
| `onAgentResponseCorrection` | Agent corrected a previous response | v1.0 |
| `onAgentChatResponsePart` | Streaming chat response chunks | v1.x |
| `onAgentReasoningResponsePart` | Streaming reasoning chunks (experimental) | v1.16.0 |
| `onAudioAlignment` | Audio-to-text alignment metadata | v1.x (fixed WebRTC v1.15.2) |
| `onGuardrailTriggered` | Guardrail fired | v1.0 |
| `onAgentTyping` | External agent typing indicator | v0.14.0 widget |
| `onExternalAgentConnected` | External agent joined | v0.14.0 widget |
| `onPing` | Server ping with latency estimate | v1.15.0 |
| `onRichContent` | Agent-sent UI components (item cards, etc.) — experimental | v1.18.0 |
| `onIncomingEvent` / `onOutgoingEvent` | Raw incoming/outgoing socket event monitoring (debug/observability) | main, unreleased |
| `onDebug` | Internal debug events | v1.0 |
