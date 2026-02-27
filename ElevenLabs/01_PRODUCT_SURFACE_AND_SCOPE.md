# 01 — Product Surface and Scope (Current)

## What is actively relevant

### 1) `@elevenlabs/client` (primary)
Use this for:
- Conversational agent sessions (`Conversation.startSession`)
- Realtime speech-to-text with Scribe (`Scribe.connect`)
- Event-driven session control and audio/device management

### 2) Framework wrappers
- `@elevenlabs/react`
- `@elevenlabs/react-native`

These are convenience layers over client capabilities.

### 3) Agents CLI package in this repo
`@elevenlabs/agents-cli` README explicitly marks it **deprecated** and directs users to the official ElevenLabs CLI repo.

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
- Need “agents as code” CLI from this package: avoid (deprecated); use official ElevenLabs CLI