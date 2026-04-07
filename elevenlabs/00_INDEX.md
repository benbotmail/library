# ElevenLabs JS/TS SDK — Current-State Reference

Last validated against upstream `elevenlabs/packages` commit: `46fed15736cf33313c2572cbdf6ca104446af66c`.

This pack documents **how the current ElevenLabs JavaScript/TypeScript SDK behaves now** (not a changelog).

## What this pack covers
- `@elevenlabs/client` v1.1.2 for agent conversations (WebSocket + WebRTC)
- Scribe real-time STT via `@elevenlabs/client`
- Practical patterns for low-latency conference subtitles + translation fan-out
- Operational guardrails (tokens, auth boundaries, failure handling)
- Multimodal client message path (`multimodal_message`) for text + file reference in a single turn
- **MAJOR UPDATE**: SDK Migration Guide for v1.0+ major release breaking changes
- **NEW**: Tool mocking support for agent development
- **NEW**: Auto-select widget language from browser locale
- **NEW**: Enhanced guardrail events with `guardrail_triggered`

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
- React wrapper: `open-source/elevenlabs/packages/react/README.md`
- React Native wrapper: `open-source/elevenlabs/packages/react-native/README.md`
- Types package: `open-source/elevenlabs/packages/types/README.md`
- AsyncAPI contract: `open-source/elevenlabs/packages/types/schemas/agent.asyncapi.yaml`
- **Migration Guide**: `open-source/elevenlabs/.agents/skills/elevenlabs:sdk-migration/SKILL.md`

## Freshness note
- **MAJOR UPDATE**: SDK v1.0+ breaking changes documented in migration guide
- Added comprehensive tool mocking support for agent development
- Enhanced widget language auto-selection from browser locale and localStorage
- New `guardrail_triggered` server events for better compliance monitoring
- Enhanced React components with improved conversation context management
- React Native SDK completely restructured for better mobile performance
- Updated ESLint configs to ESM format (.mjs)
- Improved migration tools and patterns for v1.0 upgrade

## Important scope note
For realtime STT + translation pipelines, this pack treats the **Client SDK Scribe section** as the practical source of truth for SDK behavior (events, commit strategies, token flow, and options).

## Breaking changes in v1.0+
- React Native SDK now uses modular imports instead of ElevenLabsProvider pattern
- Enhanced TypeScript types with better discrimination
- New WebSocket message types for multimodal content
- Updated conversation state management patterns