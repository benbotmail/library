# 04 — Multilingual Subtitle Architecture (Low-Latency)

This architecture is optimized for “speaker in English, subtitles in N languages quickly”.

## Reference flow

1. Ingest audio to Scribe realtime
2. Emit partial + committed transcript events
3. Translation service fans out per target language
4. Client receives:
   - `partial_draft` (replaceable)
   - `commit_final` (authoritative)

## Event contract (recommended)

```json
{
  "session_id": "conf-2026-02-27-a",
  "segment_id": "conf-2026-02-27-a:184",
  "kind": "commit_final",
  "source_lang": "en",
  "target_lang": "es",
  "text": "La orquídea requiere...",
  "source_text": "The orchid requires...",
  "t0_ms": 1740654000123,
  "t1_ms": 1740654001766
}
```

## Why two-lane subtitles are worth it

- Draft lane keeps UI feeling immediate
- Final lane fixes unstable hypotheses
- Users prefer fast updates over delayed perfection

## Segmenting strategy

- Drive final segment boundaries from committed transcript events
- Translate selected partials at capped rate (e.g., every 300–500ms)
- Never block one target language on another (independent queues)

## Domain-vocabulary stabilization for conferences

For specialist topics (orchids, medical, legal):
- Preserve named entities/scientific binomials when translating
- Add deterministic correction pass after committed English transcript
- Keep a glossary map in config (not in prompts only)

Example map snippet:
```json
{
  "P. aphrodite": "Phalaenopsis aphrodite",
  "Dendrobium nobile": "Dendrobium nobile"
}
```

## Latency instrumentation to keep

Per segment and per language:
- `audio_to_partial_ms`
- `audio_to_commit_ms`
- `commit_to_translation_ms`
- `end_to_end_visible_ms`

Without these, optimization debates become guesswork.