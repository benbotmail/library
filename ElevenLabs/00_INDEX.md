# ElevenLabs JS/TS SDK — Current-State Reference

Last validated against upstream `elevenlabs/packages` commit: `e3eec1676125e354fa108ef040cee01c810f2ab7`.

This pack documents **how the current ElevenLabs JavaScript/TypeScript SDK behaves now** (not a changelog).

## What this pack covers
- `@elevenlabs/client` for agent conversations (WebSocket + WebRTC)
- Scribe real-time STT via `@elevenlabs/client`
- Practical patterns for low-latency conference subtitles + translation fan-out
- Operational guardrails (tokens, auth boundaries, failure handling)

## Read order
1. `01_PRODUCT_SURFACE_AND_SCOPE.md`
2. `02_CONVERSATION_SESSION_PATTERNS.md`
3. `03_SCRIBE_REALTIME_STT_PATTERNS.md`
4. `04_MULTILINGUAL_SUBTITLE_ARCHITECTURE.md`
5. `05_OPERATIONS_CHECKLIST.md`

## Canonical source files
- Monorepo overview: `open-source/elevenlabs/README.md`
- Client SDK: `open-source/elevenlabs/packages/client/README.md`
- Agents CLI status: `open-source/elevenlabs/packages/agents-cli/README.md` (deprecated)

## Important scope note
For realtime STT + translation pipelines, this pack treats the **Client SDK Scribe section** as the practical source of truth for SDK behavior (events, commit strategies, token flow, and options).