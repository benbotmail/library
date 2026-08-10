# 03 — Scribe Realtime STT Patterns (`@elevenlabs/client` v1.17.0)

## Baseline conference preset (English)

```ts
import {
  Scribe,
  CommitStrategy,
  RealtimeEvents,
  AudioFormat,
} from "@elevenlabs/client";

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
```

## Microphone mode (automatic browser streaming)

```ts
const conn = Scribe.connect({
  token: scribeTokenFromBackend,
  modelId: "scribe_v2_realtime",
  microphone: {
    echoCancellation: true,
    noiseSuppression: true,
    autoGainControl: true,
  },
  commitStrategy: CommitStrategy.VAD,
});
```

The SDK handles `getUserMedia`, audio processing worklets, and automatic teardown on close/failure.

## Commit strategy choices

- `VAD`: best default for live speech where boundaries are unknown
- `MANUAL`: best when your app already controls segmentation (push-to-talk, known chunk boundaries, file slicing)

## Keyterms (model biasing)

Pass `keyterms` to bias the model toward specific vocabulary. Maximum 50 terms, each up to 20 characters.

```ts
const conn = Scribe.connect({
  token: scribeTokenFromBackend,
  modelId: "scribe_v2_realtime",
  languageCode: "en",
  keyterms: ["Phalaenopsis", "Dendrobium", "Cattleya"],
});
```

Keyterms are sent as repeated query parameters (`keyterms=term1&keyterms=term2`).

## Clean transcripts with noVerbatim

Set `noVerbatim: true` to strip filler words, false starts, and disfluencies:

```ts
const conn = Scribe.connect({
  token: scribeTokenFromBackend,
  modelId: "scribe_v2_realtime",
  languageCode: "en",
  noVerbatim: true,
});
```

## Language detection (v1.12.0)

Set `includeLanguageDetection: true` to receive detected language metadata in transcription results:

```ts
const conn = Scribe.connect({
  token: scribeTokenFromBackend,
  modelId: "scribe_v2_realtime",
  includeLanguageDetection: true,
});

conn.on(RealtimeEvents.COMMITTED_TRANSCRIPT_WITH_TIMESTAMPS, (d) => {
  console.log(`Detected language: ${d.language_code}`);
});
```

## Zero retention mode via enableLogging (v1.17.0)

Set `enableLogging: false` to run sessions in zero retention mode. History features are unavailable for the session. **Enterprise customers only.**

```ts
const conn = Scribe.connect({
  token: scribeTokenFromBackend,
  modelId: "scribe_v2_realtime",
  enableLogging: false, // zero retention mode
});
```

> **Note:** The types package renamed `disable_logging` to `enable_logging` to match the field the server actually reports on the wire (`session_started` config).

## React useScribe hook (v1.12.0)

Full Scribe options parity including `enableLogging`, `includeLanguageDetection`, `keyterms`, `noVerbatim`:

```tsx
import { useScribe } from "@elevenlabs/react";

const {
  status,
  isConnected,
  isTranscribing,
  isMuted,
  partialTranscript,
  committedTranscripts,
  error,
  connect,
  disconnect,
  mute,
  unmute,
  sendAudio,
  commit,
  clearTranscripts,
} = useScribe({
  token: "scribe_token",
  modelId: "scribe_v2_realtime",
  microphone: { echoCancellation: true },
  commitStrategy: CommitStrategy.VAD,
  includeTimestamps: true,
  includeLanguageDetection: true,
  keyterms: ["AcmeCorp"],
  noVerbatim: true,
  enableLogging: false, // enterprise zero retention
  onPartialTranscript: ({ text }) => console.log("Partial:", text),
  onCommittedTranscript: ({ text }) => console.log("Final:", text),
});
```

## Previous context for domain terms

`previousText` can improve continuity, but only in the **first** chunk:

```ts
conn.send({
  audioBase64: firstChunk,
  previousText: "Topic: orchid taxonomy. Terms: Phalaenopsis, Dendrobium.",
});
```

Do not resend later in session.

## Token flow (required security pattern)

Backend:
1. Call ElevenLabs token endpoint with permanent API key
2. Return short-lived/single-use token to frontend

Frontend:
1. Request token from your backend
2. Connect with `Scribe.connect({ token, ... })`

Never place permanent key in browser/mobile app.

## Scribe microphone reliability (v1.15.1 fixes)

The microphone subsystem now handles edge cases robustly:
- **Teardown during setup**: Closing the connection while async microphone setup is resolving no longer leaks `MediaStreamTrack`/`AudioContext`
- **Late frames**: Microphone frames arriving after socket close are dropped silently (no `WebSocket is not connected` throw)
- **Server-initiated close**: Releases microphone automatically; consumers reacting to `CLOSE` without calling `close()` won't leave a live mic
- **Setup failures**: Reported through `RealtimeEvents.ERROR` with typed error payload (same as server errors)
- **Connecting abort**: `close()` aborting a still-connecting socket no longer logs spurious `1006` errors

## Error events to treat as first-class

| Event | Constant | When |
|---|---|---|
| Generic error | `RealtimeEvents.ERROR` | Any failure including local mic errors |
| Auth error | `RealtimeEvents.AUTH_ERROR` | Expired/invalid token |
| Quota exceeded | `RealtimeEvents.QUOTA_EXCEEDED` | Billing/rate condition |
| Commit throttled | `RealtimeEvents.COMMIT_THROTTLED` | Too many commit calls |
| Transcriber error | `RealtimeEvents.TRANSCRIBER_ERROR` | Server-side transcription failure |
| Unaccepted terms | `RealtimeEvents.UNACCEPTED_TERMS` | Terms of service not accepted |
| Rate limited | `RealtimeEvents.RATE_LIMITED` | Rate limit hit |
| Input error | `RealtimeEvents.INPUT_ERROR` | Malformed input |
| Queue overflow | `RealtimeEvents.QUEUE_OVERFLOW` | Audio queue overflowed |
| Resource exhausted | `RealtimeEvents.RESOURCE_EXHAUSTED` | Server resources exhausted |
| Session time limit | `RealtimeEvents.SESSION_TIME_LIMIT_EXCEEDED` | Max session duration reached |
| Chunk size exceeded | `RealtimeEvents.CHUNK_SIZE_EXCEEDED` | Audio chunk too large |
| Insufficient audio activity | `RealtimeEvents.INSUFFICIENT_AUDIO_ACTIVITY` | Not enough audio detected |

```ts
conn.on(RealtimeEvents.ERROR, (e) => reportError(e));
conn.on(RealtimeEvents.AUTH_ERROR, (e) => reAuthAndReconnect());
conn.on(RealtimeEvents.CLOSE, () => cleanupPipeline());
```

Operationally: re-auth then reconnect with backoff; preserve pending segment IDs in your app-level pipeline.
