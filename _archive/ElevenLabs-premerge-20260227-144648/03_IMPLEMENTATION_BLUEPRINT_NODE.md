# 03 — Implementation Blueprint (Node.js)

## Goal
Build a service that behaves like "simultaneous transcription + translation":
- ingest live audio
- receive low-latency transcript updates from ElevenLabs Realtime STT
- emit translated text stream with stable segment commits

---

## 1) Realtime STT session setup

Endpoint:
- `wss://api.elevenlabs.io/v1/speech-to-text/realtime`

Key query params:
- `model_id=scribe_v2_realtime`
- `audio_format=pcm_16000`
- `commit_strategy=vad` (or `manual`)
- `include_timestamps=true` (optional)
- `include_language_detection=true` (optional)

Auth options:
- Server-side trusted: `xi-api-key` header
- Browser/client-side safer: create single-use token (`realtime_scribe`) via `/v1/single-use-token/{token_type}` and pass `token=...` query param

---

## 2) Event handling model

From docs, expect message types such as:
- `session_started`
- `partial_transcript`
- `committed_transcript`
- `committed_transcript_with_timestamps`
- error classes (`error`, `auth_error`, `quota_exceeded`)

Recommended handling:
- Render partial transcript in gray/ephemeral UI
- Only feed committed segments into translator
- Attach segment IDs + timing metadata

---

## 3) Minimal Node pseudo-code

```ts
import WebSocket from 'ws';

const ws = new WebSocket(
  'wss://api.elevenlabs.io/v1/speech-to-text/realtime?model_id=scribe_v2_realtime&audio_format=pcm_16000&commit_strategy=vad&include_timestamps=true',
  { headers: { 'xi-api-key': process.env.ELEVENLABS_API_KEY! } }
);

const committedBuffer: string[] = [];

ws.on('message', async (raw) => {
  const msg = JSON.parse(raw.toString());

  if (msg.message_type === 'partial_transcript') {
    emitUI({ type: 'partial', text: msg.text });
    return;
  }

  if (msg.message_type === 'committed_transcript' ||
      msg.message_type === 'committed_transcript_with_timestamps') {
    const sourceText = msg.text?.trim();
    if (!sourceText) return;

    committedBuffer.push(sourceText);
    const context = committedBuffer.slice(-4).join(' '); // rolling context

    const translated = await translateText({
      text: sourceText,
      context,
      sourceLang: msg.language_code ?? 'auto',
      targetLang: 'en'
    });

    emitUI({
      type: 'commit',
      sourceText,
      translatedText: translated,
      words: msg.words ?? undefined
    });
  }
});

function sendAudioChunk(base64Pcm16k: string) {
  ws.send(JSON.stringify({
    audio_base_64: base64Pcm16k,
    sample_rate: 16000
  }));
}
```

---

## 4) Translator contract

Keep this provider-agnostic:

```ts
type TranslateInput = {
  text: string;
  context?: string;
  sourceLang?: string; // 'auto' | ISO code
  targetLang: string;
};
```

Guidelines:
- preserve punctuation and named entities
- avoid translating hesitations unless requested
- support glossary/term lock for domain-specific words

---

## 5) Commit strategy guidance

### VAD commit
Use for microphone/live conversation.
Pros: automatic segmentation.
Cons: unstable in very noisy channels.

### Manual commit
Use for deterministic chunking from telephony/media pipelines.
Pros: precise control.
Cons: more orchestration burden.

Docs note:
- avoid rapid repeated manual commits (can hurt performance)
- first transcript processing begins after ~2 seconds of audio
- auto-commit occurs by default periodically in manual mode

---

## 6) Production hardening checklist

- Reconnect with backoff and pass `previous_text` on first chunk after reconnect
- Dead-letter queue for failed translation segments
- Per-session budget guardrails (max translated chars/min)
- Structured logging: segment_id, stt_latency_ms, translation_latency_ms
- Metrics: WER proxy, end-to-end latency p50/p95, drop/reconnect rates
