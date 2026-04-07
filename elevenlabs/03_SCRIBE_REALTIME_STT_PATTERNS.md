# 03 — Scribe Realtime STT Patterns

## Baseline conference preset (English)

```ts
import { Scribe, CommitStrategy, RealtimeEvents, AudioFormat } from "@elevenlabs/client";

const conn = Scribe.connect({
  token: scribeTokenFromBackend,
  modelId: "scribe_v2_realtime",
  languageCode: "en",
  includeTimestamps: true,

  // For streamed/custom audio mode:
  audioFormat: AudioFormat.PCM_16000,
  sampleRate: 16000,
  commitStrategy: CommitStrategy.VAD,
  vadThreshold: 0.5,
  vadSilenceThresholdSecs: 0.5,
  minSpeechDurationMs: 100,
  minSilenceDurationMs: 500,
});

conn.on(RealtimeEvents.PARTIAL_TRANSCRIPT, d => handlePartial(d));
conn.on(RealtimeEvents.COMMITTED_TRANSCRIPT, d => handleCommitted(d));
conn.on(RealtimeEvents.COMMITTED_TRANSCRIPT_WITH_TIMESTAMPS, d => handleTimed(d));
conn.on(RealtimeEvents.ERROR, e => reportError(e));
```

## Commit strategy choices

- `VAD`: best default for live speech where boundaries are unknown
- `MANUAL`: best when your app already controls segmentation (push-to-talk, known chunk boundaries, file slicing)

Rule of thumb:
- Live conference mic: start with `VAD`
- Pre-cut media/transcodes: prefer `MANUAL` + explicit `commit()`

## Previous context for domain terms

`previousText` can improve continuity, but only in the **first** chunk.

```ts
conn.send({
  audioBase64: firstChunk,
  previousText: "Topic: orchid taxonomy and cultivation. Terms include Phalaenopsis, Dendrobium, Cattleya.",
});
```

Do not resend later in session; API returns error behavior for non-first-chunk use.

## Token flow (required security pattern)

Backend:
1. Call ElevenLabs token endpoint with permanent API key
2. Return short-lived/single-use token to frontend

Frontend:
1. Request token from your backend
2. Connect with `Scribe.connect({ token, ... })`

Never place permanent key in browser/mobile app.

## Error events to treat as first-class

- `ERROR` (generic failure path)
- `AUTH_ERROR` (expired/invalid token)
- `QUOTA_EXCEEDED` (billing/rate condition)
- `CLOSE` (connection teardown)

Operationally, re-auth then reconnect with backoff; preserve pending segment IDs in your app-level pipeline.