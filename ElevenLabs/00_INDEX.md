# ElevenLabs JS/TS SDK — Current-State Reference

Last validated against upstream `elevenlabs/packages` commit: `a00f0a4d276c9601e232794dae04987c6c0fabfd`.

This pack documents **how the current ElevenLabs JavaScript/TypeScript SDK behaves now** (not a changelog).

## What this pack covers
- `@elevenlabs/client` for agent conversations (WebSocket + WebRTC)
- Scribe real-time STT via `@elevenlabs/client`
- Practical patterns for low-latency conference subtitles + translation fan-out
- Operational guardrails (tokens, auth boundaries, failure handling)
- New multimodal client message path (`multimodal_message`) for text + file reference in a single turn

## Read order
1. `01_PRODUCT_SURFACE_AND_SCOPE.md`
2. `02_CONVERSATION_SESSION_PATTERNS.md`
3. `03_SCRIBE_REALTIME_STT_PATTERNS.md`
4. `04_MULTILINGUAL_SUBTITLE_ARCHITECTURE.md`
5. `05_OPERATIONS_CHECKLIST.md`
6. `06_REFERENCE_CONFIGS_AND_PIPELINE.md`
7. `07_EVALUATION_AND_ACCEPTANCE.md`
8. `08_API_ENDPOINTS_AND_ERROR_SURFACE_APPENDIX.md`
9. `09_VERSION_PINNING_AND_COMPATIBILITY_MATRIX.md`

## Canonical source files
- Monorepo overview: `open-source/elevenlabs/README.md`
- Client SDK: `open-source/elevenlabs/packages/client/README.md`
- React wrapper: `open-source/elevenlabs/packages/react/README.md`
- React Native wrapper: `open-source/elevenlabs/packages/react-native/README.md`
- Types package: `open-source/elevenlabs/packages/types/README.md`
- AsyncAPI contract: `open-source/elevenlabs/packages/types/schemas/agent.asyncapi.yaml`

## Freshness note
- This upstream revision adds first-class `multimodal_message` support in client + types + schema.
- Audio worklet resampling handling is now shared for input and output paths via a dedicated utility.

## Important scope note
For realtime STT + translation pipelines, this pack treats the **Client SDK Scribe section** as the practical source of truth for SDK behavior (events, commit strategies, token flow, and options).
