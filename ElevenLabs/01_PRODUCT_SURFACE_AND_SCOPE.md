# 01 — Product Surface and Scope (Current)

## What is actively relevant

### 1) `@elevenlabs/client` (primary)
Use this for:
- Conversational agent sessions (`Conversation.startSession`)
- Realtime speech-to-text with Scribe (`Scribe.connect`)
- Event-driven session control and audio/device management

### 2) Framework + integration packages
- `@elevenlabs/react`
- `@elevenlabs/react-native`
- `@elevenlabs/types`
- `@elevenlabs/convai-widget-core`
- `@elevenlabs/convai-widget-embed`

These are convenience/integration layers over client capabilities.

### 3) Package-surface freshness note
In the currently tracked upstream commit (`f61e728...`), `packages/agents-cli` is no longer present in this monorepo.
Treat any older references to `@elevenlabs/agents-cli` here as historical only.

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
- Scribe `previousText` is only valid in the **first** audio chunk of a session.
- For conference subtitles, treat partials as provisional and committed transcripts as authoritative.

---

## Minimal decision matrix

- Need full voice agent UX (turn-taking + TTS + tools): use `Conversation.startSession`
- Need high-control realtime transcription feed: use `Scribe.connect`
- Need browser frontend with strict key isolation: server-minted tokens only
- Need embedded web widget UX: evaluate `convai-widget-*` packages
- Need strictly typed shared event contracts across services: use `@elevenlabs/types`