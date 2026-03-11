# 01 — Product Surface and Scope (Current)

## What is actively relevant

### 1) `@elevenlabs/client` (primary)
Use this for:
- Conversational agent sessions (`Conversation.startSession`)
- Realtime speech-to-text with Scribe (`Scribe.connect`)
- Event-driven session control and audio/device management
- Multimodal turn submission via `sendMultimodalMessage({ text?, fileId? })`

### 2) Framework + integration packages
- `@elevenlabs/react`
- `@elevenlabs/react-native`
- `@elevenlabs/types`
- `@elevenlabs/convai-widget-core`
- `@elevenlabs/convai-widget-embed`

These are convenience/integration layers over client capabilities.

### 3) Package-surface freshness note
In the currently tracked upstream commit (`a00f0a4...`), major surface-relevant changes are:
- `multimodal_message` added to outbound conversation event contracts
- React hook surface exposes `sendMultimodalMessage`
- Audio resampling helper moved to shared utility (`addLibsamplerateModule`) and reused by input/output

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

---

## Minimal decision matrix

- Need full voice agent UX (turn-taking + TTS + tools): use `Conversation.startSession`
- Need text+file turn handoff in one request: use `sendMultimodalMessage`
- Need high-control realtime transcription feed: use `Scribe.connect`
- Need browser frontend with strict key isolation: server-minted tokens only
- Need embedded web widget UX: evaluate `convai-widget-*` packages
- Need strictly typed shared event contracts across services: use `@elevenlabs/types`
