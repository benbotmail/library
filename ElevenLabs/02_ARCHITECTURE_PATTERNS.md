# 02 — Architecture Patterns for Simultaneous Transcription + Translation

## Pattern A (recommended): Realtime STT + Streaming Translation

### Flow
Client mic/audio -> ElevenLabs Realtime STT WS -> partial/committed transcript events -> translation service -> UI + optional TTS

### Why this is the right default
- Lowest latency for live scenarios
- You control segmentation policy and translation quality/cost tradeoff
- Easy to support multiple target languages per source stream

### Core protocol choices
- `model_id=scribe_v2_realtime`
- `audio_format=pcm_16000` (recommended)
- `commit_strategy=vad` for conversational mic input
- `include_timestamps=true` if subtitle alignment matters

### Segment policy (key to quality)
- Translate **committed** chunks (not every partial)
- Keep rolling context window (2–6 previous committed segments)
- Re-translate last N chars when new punctuation arrives (optional)

---

## Pattern B: Batch STT + Post Translation (faster integration, higher latency)

### Flow
Upload audio -> `/v1/speech-to-text` -> full transcript -> translate paragraph/sentence chunks

### Use when
- Voice notes, meetings, podcast ingestion
- Latency tolerance: seconds to minutes
- You need richer extraction options (entity detection, diarization)

---

## Pattern C: Dubbing pipeline (translation + revoicing of media)

### Flow
Upload audio/video -> `/v1/dubbing` -> poll `/v1/dubbing/{id}` -> fetch dubbed media `/v1/dubbing/{id}/audio/{language_code}`

### Use when
- Localization deliverable is audio/video itself
- You care about speaker style and timing preservation
- Real-time interactivity is not required

---

## Practical hybrid for Telegram voice notes
1. For short notes (<~2 min): Pattern B (batch STT) + translator + text reply
2. For live calls/streams: Pattern A (realtime STT)
3. For media publishing: Pattern C (dubbing)
