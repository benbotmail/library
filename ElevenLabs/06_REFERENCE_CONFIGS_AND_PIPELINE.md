# 06 — Reference Configs and Pipeline Blueprint

This document gives a practical, implementation-ready baseline for production speech->subtitle systems using `@elevenlabs/client` + downstream translation workers.

## 1) Minimal environment contract

```bash
# Backend only
ELEVENLABS_API_KEY=...

# App/runtime
SCRIBE_MODEL_ID=scribe_v2_realtime
SOURCE_LANGUAGE=en
TARGET_LANGUAGES=es,fr,de,ar,ja
PARTIAL_TRANSLATION_INTERVAL_MS=400
TRANSLATION_TIMEOUT_MS=1800
MAX_RECONNECT_ATTEMPTS=8
```

Guidelines:
- Keep permanent API keys backend-only.
- Treat all client tokens as short-lived and one-time use where supported.
- Keep target languages configurable per event/session.

---

## 2) Backend token endpoints (shape)

### Conversation token/signed-url endpoint
`POST /api/elevenlabs/conversation-token`

Response (example):
```json
{
  "kind": "webrtc_token",
  "token": "...",
  "expires_at": 1740657600
}
```

### Scribe token endpoint
`POST /api/elevenlabs/scribe-token`

Response (example):
```json
{
  "token": "...",
  "expires_at": 1740657600,
  "model_id": "scribe_v2_realtime"
}
```

Operational notes:
- Protect endpoints with app auth/session.
- Add per-user/session rate limits.
- Log issuance with redaction (never log full token).

---

## 3) Realtime pipeline stages

1. **Audio Ingest** -> browser/native app
2. **STT Session** -> Scribe partial/committed events
3. **Segment Bus** -> normalize event shape + dedupe key
4. **Translation Fan-out** -> one queue/worker per target language
5. **Subtitle Emitter** -> partial updates + final replacements
6. **Store** -> append authoritative transcript timeline

Recommended dedupe key:
`session_id + segment_id + target_lang + kind`

---

## 4) Event normalization schema

```json
{
  "session_id": "string",
  "segment_id": "string",
  "kind": "partial_draft | commit_final",
  "source_lang": "en",
  "target_lang": "es",
  "text": "string",
  "source_text": "string",
  "t0_ms": 0,
  "t1_ms": 0,
  "revision": 1,
  "producer_ts_ms": 0
}
```

Rules:
- `commit_final` is immutable for UI correctness.
- `partial_draft` may replace same `segment_id` repeatedly.
- Keep source transcript and translation in the same event for audit/debug.

---

## 5) Queue/worker policy

Per-language independent workers:
- avoids head-of-line blocking across languages
- allows different timeout/retry policies per language

Suggested defaults:
- partial queue: drop older drafts for same segment when a newer draft arrives
- final queue: never drop; retry with bounded backoff
- translation timeout: 1.5–2.0s (then fallback to source text marker)

Fallback marker example:
`[untranslated: es] <source_text>`

---

## 6) Reconnect and recovery policy

On `AUTH_ERROR` or token expiry:
1. pause sends
2. fetch new token
3. reconnect
4. replay only uncommitted local buffered audio/segments if applicable

On generic transport close:
- exponential backoff with jitter
- hard stop after `MAX_RECONNECT_ATTEMPTS`
- emit health alert for operator visibility

---

## 7) Storage model (authoritative timeline)

Persist final transcript entries as append-only records:

```json
{
  "session_id": "conf-2026-02-27-a",
  "segment_id": "...",
  "source_final": "The orchid requires...",
  "translations": {
    "es": "La orquídea requiere...",
    "fr": "L'orchidée nécessite..."
  },
  "t0_ms": 1740654000123,
  "t1_ms": 1740654001766,
  "created_at": "2026-02-27T10:00:01.900Z"
}
```

Why append-only:
- preserves incident replay
- enables deterministic QA and post-event correction
- avoids silent history mutation

---

## 8) UI contract for subtitle clients

- Render partials in lighter style (or italics)
- Replace with committed final when available
- Keep per-language lag indicators (green/yellow/red)
- Allow user fallback to source language if translation is stale

---

## 9) Fast production sanity checks

Before launch:
- 30-minute real audio run with packet loss simulation
- token expiry test during active speaking
- reconnect while preserving segment order
- per-language timeout behavior validated
- glossary enforcement spot-check on specialist terms

If all pass with stable p95 latency and no segment corruption, the pipeline is production-ready for pilot.