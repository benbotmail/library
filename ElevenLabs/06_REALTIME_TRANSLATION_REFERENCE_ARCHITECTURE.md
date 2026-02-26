# 06 — Realtime Translation Reference Architecture (Production)

Use this as the **default architecture** for near-real-time multilingual transcription + translation.

## Scope
- Input: live audio stream (mic, call bridge, media stream)
- Output: source transcript + translated transcript stream (optional translated audio)
- Latency target: low hundreds of ms for transcript events; translation output typically slightly higher

---

## 1) System components

1. **Ingest Gateway**
   - Receives audio frames from client/bridge
   - Normalizes to mono PCM and target sample rate (prefer `pcm_16000`)
   - Adds session metadata (tenant, user, language hints)

2. **STT Streamer (ElevenLabs Realtime)**
   - WebSocket to `/v1/speech-to-text/realtime`
   - `model_id=scribe_v2_realtime`
   - Commit strategy: `vad` for conversational input, `manual` for deterministic pipelines

3. **Segment Orchestrator**
   - Buffers partials for UI only
   - Promotes committed segments to translation queue
   - Maintains rolling context window (last N committed segments)

4. **Translation Layer**
   - Provider-agnostic adapter (LLM or MT engine)
   - Supports target fan-out (e.g., en + fr + ar)
   - Enforces glossary/term lock and style policy

5. **Delivery Layer**
   - Emits structured events to UI / chat / webhook
   - Optional TTS of translated segments
   - Applies sequencing and de-duplication

6. **Observability + Controls**
   - Metrics, logs, traces
   - Quotas and budget controls
   - Retry, dead-letter handling

---

## 2) Canonical event contract

Use a stable internal schema so downstream systems do not depend on vendor-specific payload shape.

```json
{
  "session_id": "string",
  "segment_id": "string",
  "event_type": "partial|commit|translation|error",
  "source": {
    "text": "string",
    "language_code": "string",
    "words": []
  },
  "translation": {
    "target_lang": "string",
    "text": "string"
  },
  "timing": {
    "audio_start_ms": 0,
    "audio_end_ms": 0,
    "stt_latency_ms": 0,
    "translation_latency_ms": 0,
    "e2e_latency_ms": 0
  },
  "meta": {
    "provider": "elevenlabs",
    "model_id": "scribe_v2_realtime"
  }
}
```

Notes:
- Translate **committed** text only.
- Keep `segment_id` deterministic (`session + commit_index`) for idempotency.

---

## 3) Language strategy

### Source language
- If known: pass `language_code` explicitly.
- If unknown/mixed: enable language detection and store per-commit detected language.

### Target language fan-out
- For multiple targets, run translation calls in parallel.
- Keep one shared context window per source stream.
- Do not let a slow target block fast targets.

### Code-switching
- Expect source language drift mid-session.
- Recompute translation prompt constraints when detected language changes.

---

## 4) Latency budget decomposition

Track at least these 4 buckets:
1. Audio capture + upload
2. STT processing
3. Translation
4. Delivery/render

Practical target envelope (interactive UX):
- STT partial updates: sub-second
- STT committed segment: low hundreds of ms after speech boundary
- Commit → translated text: typically <= ~1s depending on translator/model and context size

---

## 5) Failure model and recovery

### STT websocket disconnect
- Reconnect with exponential backoff + jitter.
- On first new chunk after reconnect, send short `previous_text` context.
- Resume commit index from last durable checkpoint.

### Translation provider timeout
- Retry with short bounded backoff.
- If still failing, emit source-only commit and mark translation status `degraded`.

### Out-of-order delivery
- Always sequence by `segment_id` and `commit_index`.
- Keep reorder buffer in clients (small, bounded).

### Quota/auth errors
- Handle `auth_error` and `quota_exceeded` explicitly.
- Surface actionable error states to operator dashboards.

---

## 6) Security and key management

- Server-to-server: use `xi-api-key` only on trusted backend.
- Browser/client capture: mint single-use token (`realtime_scribe`) and avoid exposing long-lived keys.
- Minimize transcript retention by policy; enable strict logging controls where required.
- Separate tenant namespaces for keys, quotas, and storage.

---

## 7) Minimal acceptance checks (before production)

- [ ] Handles 10+ minute session without memory growth issues
- [ ] Reconnect recovers without losing ordering
- [ ] Translation fan-out works for >=2 target languages concurrently
- [ ] p95 commit→translation latency within your SLO
- [ ] Quota/auth errors are visible and user-safe
- [ ] Glossary lock preserves required domain terms

---

## 8) Recommended default profile

- `model_id=scribe_v2_realtime`
- `audio_format=pcm_16000`
- `commit_strategy=vad` (mic) / `manual` (telephony/media bridges)
- `include_timestamps=true` when subtitles or alignment needed
- Translate only committed segments with rolling context (2–6 recent commits)
