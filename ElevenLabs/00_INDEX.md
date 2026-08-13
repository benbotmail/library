# ElevenLabs JS/TS SDK — Current-State Reference

Last validated against upstream `elevenlabs/packages` commit: `78cc4c1bc059d25559db3f059ea8e350ff8b62f0` (2026-08-13).

This pack documents **how the current ElevenLabs JavaScript/TypeScript SDK behaves now** (not a changelog).

## Current package versions documented

| Package | Version |
|---|---|
| `@elevenlabs/client` | 1.17.0 |
| `@elevenlabs/react` | 1.12.0 |
| `@elevenlabs/react-native` | 1.2.18 |
| `@elevenlabs/types` | 0.19.0 |
| `@elevenlabs/convai-widget-core` | 0.15.1 |
| `@elevenlabs/convai-widget-embed` | 0.15.1 |

## What this pack covers
- `@elevenlabs/client` v1.17.0 for agent conversations (WebSocket + WebRTC, text + voice)
- Full callback surface including `onAgentReasoningResponsePart`, `onPing`, `onAudioAlignment`, `onAgentTyping`, `onExternalAgentConnected`, `onRichContent` (experimental)
- Scribe real-time STT with `enableLogging`, `includeLanguageDetection`, `keyterms`, `noVerbatim`
- Scribe `workletPaths.scribeAudioProcessor` for self-hosting audio worklets under strict CSP
- `sendFeedback` with per-message targeting and null-clearing
- `overrides.asr.keywords` for per-conversation ASR biasing
- `textOnly` mode for text-only agent sessions
- Self-hosted orchestrator sessions (experimental) — route conversations to private deployments
- Platform abstraction layer (`platform/web/`, `internal/unity`)
- Unity WebGL bridge (`@elevenlabs/client/internal/unity`)
- Multimodal user turns via `sendMultimodalMessage({ text?, fileId? })` + `uploadFile()`
- Widget config: `show_resize_button`, file upload, `EventBridge`, audio tags, dynamic variables
- Widget markdown rendering in voice transcripts (fixed)
- React `useScribe` hook with full Scribe options parity
- React `ConversationProvider` state consistency through disconnect (fixed)
- Practical patterns for low-latency conference subtitles + translation fan-out
- Operational guardrails (tokens, auth boundaries, failure handling)

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
10. `10_SDK_MIGRATION_V1_PREVIEW.md`

## Canonical source files
- Monorepo overview: `open-source/elevenlabs/README.md`
- Client SDK: `open-source/elevenlabs/packages/client/README.md`
- Client types: `open-source/elevenlabs/packages/client/src/types.ts`
- React wrapper: `open-source/elevenlabs/packages/react/README.md`
- React Native wrapper: `open-source/elevenlabs/packages/react-native/README.md`
- Types package: `open-source/elevenlabs/packages/types/README.md`
- AsyncAPI contract: `open-source/elevenlabs/packages/types/schemas/agent.asyncapi.yaml`
- Scribe AsyncAPI: `open-source/elevenlabs/packages/types/schemas/scribe.asyncapi.yaml`
- Widget config types: `open-source/elevenlabs/packages/convai-widget-core/src/types/config.ts`

## Important scope note
For realtime STT + translation pipelines, this pack treats the **Client SDK Scribe section** as the practical source of truth for SDK behavior (events, commit strategies, token flow, and options).
