# ElevenLabs JS/TS SDK — Current-State Reference

Last validated against upstream `elevenlabs/packages` commit: `2a9057eec7d18709306e3831385fc6e300f18b41` (2026-08-22).

This pack documents **how the current ElevenLabs JavaScript/TypeScript SDK behaves now** (not a changelog).

## Current package versions documented

| Package | Version |
|---|---|
| `@elevenlabs/client` | 1.21.0 |
| `@elevenlabs/react` | 1.13.0 |
| `@elevenlabs/react-native` | 1.2.23 |
| `@elevenlabs/types` | 0.21.1 |
| `@elevenlabs/convai-widget-core` | 0.16.4 |
| `@elevenlabs/convai-widget-embed` | 0.16.4 |

> Note: the `onIncomingEvent`/`onOutgoingEvent` monitoring callbacks **are present in released code since 1.20.0** (they ship in the `Callbacks` type even though no changelog entry highlights them). The following exist on `main` but are **not yet in a released version**: `onMCPToolApprovalRequest` (MCP tool approval handler), `onExternalAgentDisconnected`, WebRTC hard-fail when the initiation payload can't be published, and widget queue-status / first-message rich content / `show_language_selector_on_trigger` / text-mode first message. For release highlights see `09_VERSION_PINNING_AND_COMPATIBILITY_MATRIX.md`.

## What this pack covers
- `@elevenlabs/client` v1.21.0 for agent conversations (WebSocket + WebRTC, text + voice)
- Full callback surface including `onAgentReasoningResponsePart`, `onPing`, `onAudioAlignment`, `onAgentTyping`, `onExternalAgentConnected`, `onRichContent` (experimental), and **`onContextUsage`** (v1.21.0 — context-window budget per agent turn)
- Scribe real-time STT with `enableLogging`, `includeLanguageDetection`, `keyterms`, `noVerbatim`
- Scribe (v1.20.0): `secondaryLanguages`, `entityDetection` (+ `committed_transcript_entities` event), `filterBackgroundAudio`, `final_transcript` / `final_transcript_with_timestamps` / `invalid_request` events, widened `Word` timestamps shape, retriable mic-permission failures
- `webRtc.iceTransportPolicy` session option (v1.20.0) for TURN-only WebRTC on UDP-blocked networks
- Scribe `workletPaths.scribeAudioProcessor` for self-hosting audio worklets under strict CSP
- `sendFeedback` with per-message targeting and null-clearing
- `overrides.asr.keywords` for per-conversation ASR biasing
- `textOnly` mode for text-only agent sessions
- Self-hosted orchestrator sessions (experimental) — route conversations to private deployments
- Widget self-hosted orchestrator via `orchestrator-url` / `orchestrator-agent-config` attributes (0.16.0, experimental)
- Widget rich content rendering — agent-sent `buttons` (quick replies) rendered inline in transcript with validation + graceful fallback
- **MCP tool-call approvals** via `onMCPToolApprovalRequest` — SDK-mediated request/response with per-call AbortSignal (main, unreleased; target: next client minor)
- `onIncomingEvent` / `onOutgoingEvent` connection monitoring callbacks (in released code since 1.20.0)
- `onExternalAgentDisconnected` callback — AI agent resumes control when a live human agent leaves (main, unreleased)
- Platform abstraction layer (`platform/web/`, `internal/unity`)
- Unity WebGL bridge (`@elevenlabs/client/internal/unity`)
- Multimodal user turns via `sendMultimodalMessage({ text?, fileId? })` + `uploadFile()`
- Widget config: `show_resize_button`, file upload, `EventBridge`, audio tags, dynamic variables
- Widget concurrency wait-queue UX: waiting status, blocked sends while queued, friendly queue-timeout message (main, unreleased)
- Widget `show_language_selector_on_trigger` config (default `true`) — hide the language dropdown on the collapsed launcher only (main, unreleased)
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
