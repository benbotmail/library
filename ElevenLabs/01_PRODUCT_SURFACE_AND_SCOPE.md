# 01 — Product Surface and Scope (Current)

## What is actively relevant

### 1) `@elevenlabs/client` v1.1.2 (primary)
Use this for:
- Conversational agent sessions (`Conversation.startSession`)
- Realtime speech-to-text with Scribe (`Scribe.connect`)
- Event-driven session control and audio/device management
- Multimodal turn submission via `sendMultimodalMessage({ text?, fileId? })`
- **NEW**: Tool mocking support for agent development
- **NEW**: Enhanced guardrail events with `guardrail_triggered`
- **NEW**: WebSocket event structure improvements

### 2) Framework + integration packages v1.0.3
- `@elevenlabs/react` - **MAJOR UPDATE**: Completely restructured components
- `@elevenlabs/react-native` - **MAJOR UPDATE**: Modular imports, provider pattern removed
- `@elevenlabs/types` v0.9.1 - Enhanced with type discriminants
- `@elevenlabs/convai-widget-core` v0.11.2 - Auto-language selection
- `@elevenlabs/convai-widget-embed` v0.11.2 - Keep in sync with core

### 3) Package-surface freshness note
In the currently tracked upstream commit (`46fed15...`), major surface-relevant changes are:
- **MAJOR UPDATE**: SDK v1.0+ breaking changes (see migration guide)
- **NEW**: Tool mocking support for agent development and testing
- **NEW**: Auto-select widget language from browser locale and localStorage
- **NEW**: Enhanced guardrail events with `guardrail_triggered` server events
- **NEW**: Modular React Native SDK structure (provider pattern removed)
- **NEW**: Enhanced React conversation context management
- **NEW**: WebSocket message types for multimodal content
- **UPDATED**: ESLint configs migrated to ESM format (.mjs)
- **UPDATED**: Improved migration tools and patterns for v1.0 upgrade

---

## Connection/auth model you should assume

### Conversation sessions
- **Public agent:** client can start with `agentId`
- **Private agent:** your server must mint either:
  - signed URL (WebSocket path), or
  - conversation token (WebRTC path)

### Scribe realtime STT
- Client uses **single-use token** from server endpoint
- Server calls ElevenLabs token endpoint using secret API key
- Client never receives your permanent API key

---

## Current constraints that matter in architecture

- WebRTC conversation mode uses fixed `pcm_48000` behavior for input/output audio path details in device switching notes.
- Browser/device sample-rate mismatch is now explicitly handled by loading libsamplerate worklet when needed.
- Scribe `previousText` is only valid in the **first** audio chunk of a session.
- For conference subtitles, treat partials as provisional and committed transcripts as authoritative.
- **NEW**: Tool mocking configuration affects agent response patterns during development
- **NEW**: Auto-language selection from browser locale enhances user experience
- **NEW**: Enhanced error handling with guardrail events for compliance monitoring

---

## Migration requirements for v1.0+

⚠️ **Breaking Changes**: Major version upgrade required with migration
- React Native SDK: Switch from ElevenLabsProvider to modular imports
- Client SDK: Enhanced WebSocket event structure and conversation management
- Types: Updated discrimination patterns and event contracts
- React: New component architecture with ConversationProvider requirement

**Action**: Follow migration guide in `10_SDK_MIGRATION_V1_PREVIEW.md` before upgrading.

---

## Development patterns

### Tool mocking (new in v1.0+)
```ts
// Configure tool mocking for development
conversation.mockAgentTool("get_weather", (params) => {
  return `Weather in ${params.city} is 72°F and sunny.`;
});

// Enable/disable mocking
conversation.setMockingEnabled(true);
```

### Auto-language selection (new in v1.0+)
Widget automatically detects browser language and falls back to localStorage setting.

### Enhanced guardrail monitoring
```ts
conversation.on("guardrail_triggered", (event) => {
  console.log("Guardrail triggered:", event.type, event.message);
  // Handle compliance events
});
```