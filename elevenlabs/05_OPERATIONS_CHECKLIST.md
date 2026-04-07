# 05 — Operations Checklist (Current Behavior)

## Security and auth
- [ ] API key only on backend
- [ ] Client uses server-minted signed URL/conversation token/scribe token
- [ ] Token endpoint protected by your app auth

## Session reliability
- [ ] Handle `onError` / `ERROR` / `AUTH_ERROR` / `CLOSE`
- [ ] Reconnect with exponential backoff
- [ ] Persist session + segment IDs for idempotent UI replay

## Audio/transcription quality
- [ ] Force `languageCode: "en"` when source language is known
- [ ] Use VAD defaults first, then tune with real conference recordings
- [ ] Enable `includeTimestamps` when subtitle timing matters
- [ ] Use `previousText` only in first chunk when context is needed

## Translation fan-out
- [ ] Per-language queue isolation
- [ ] Draft + final subtitle semantics
- [ ] Timeout/fallback policy per target language

## Observability
- [ ] Collect p50/p95 latency by stage
- [ ] Track transcript revision rate (partial->final churn)
- [ ] Track domain-term recall (e.g., orchid species names)

## Migration notes for this repo surface
- `@elevenlabs/client` is the primary SDK for the patterns above.
- In the currently tracked monorepo revision, `packages/agents-cli` is absent. Treat older references as historical and use official ElevenLabs CLI/docs for CLI-first workflows.

## Done definition for production readiness
A release is ready when:
1. 30–60 min representative conference audio passes with stable p95 latency targets
2. Domain term recall is acceptable on committed transcripts
3. Reconnect tests and token-expiry tests pass without subtitle pipeline corruption