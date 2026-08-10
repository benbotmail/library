# 05 — Operations Checklist (Current Behavior)

## Security and auth
- [ ] API key only on backend
- [ ] Client uses server-minted signed URL/conversation token/scribe token
- [ ] Token endpoint protected by your app auth
- [ ] Scribe `enableLogging: false` for privacy-sensitive sessions (enterprise only)

## Session reliability
- [ ] Handle `onError` / `ERROR` / `AUTH_ERROR` / `CLOSE`
- [ ] Reconnect with exponential backoff
- [ ] Persist session + segment IDs for idempotent UI replay
- [ ] Wire `onPing` for connection latency monitoring
- [ ] Use `onDisconnect` details (`reason: "error" | "agent" | "user"`) for smart retry logic

## Audio/transcription quality
- [ ] Force `languageCode: "en"` when source language is known
- [ ] Use VAD defaults first, then tune with real conference recordings
- [ ] Enable `includeTimestamps` when subtitle timing matters
- [ ] Enable `includeLanguageDetection` when multi-language support needed
- [ ] Use `keyterms` for domain vocabulary (max 50 terms, ≤20 chars each)
- [ ] Use `noVerbatim: true` for clean caption/subtitle output
- [ ] Use `previousText` only in first chunk when context is needed

## Conversation agent quality
- [ ] Use `overrides.asr.keywords` for per-conversation ASR biasing
- [ ] Wire `onAudioAlignment` for lip-sync / karaoke highlighting (works on WebRTC now)
- [ ] Wire `onAgentReasoningResponsePart` if showing reasoning traces (experimental)
- [ ] Use `sendFeedback(true/false, eventId?)` for per-message rating
- [ ] Use `sendFeedback(null, eventId)` to clear feedback
- [ ] Test `toolMockConfig` for development workflows
- [ ] Use `dynamicVariables` for personalized first messages

## Widget operations
- [ ] Configure `show_resize_button` based on UI requirements (default: true)
- [ ] Configure `file_input_config` if enabling file uploads
- [ ] Test `EventBridge` for widget-to-host page communication
- [ ] Verify `strip_audio_tags` for clean transcript display

## Translation fan-out
- [ ] Per-language queue isolation
- [ ] Draft + final subtitle semantics
- [ ] Timeout/fallback policy per target language

## Observability
- [ ] Collect p50/p95 latency by stage
- [ ] Track transcript revision rate (partial→final churn)
- [ ] Track domain-term recall (e.g., orchid species names)
- [ ] Monitor `onPing` latency for connection health dashboards

## Migration notes
- Manual types (`Role`, `Mode`, `Status`, `Callbacks`, `CALLBACK_KEYS`, `DisconnectionDetails`, `MessagePayload`, `AudioAlignmentEvent`) now imported from `@elevenlabs/client`, not `@elevenlabs/types`
- `@elevenlabs/types` contains only generated code

## Done definition for production readiness
A release is ready when:
1. 30–60 min representative conference audio passes with stable p95 latency targets
2. Domain term recall is acceptable on committed transcripts
3. Reconnect tests and token-expiry tests pass without subtitle pipeline corruption
4. Scribe microphone teardown/reconnect scenarios pass without resource leaks
5. `onError` handler doesn't crash on malformed error payloads (defensive handling verified)
