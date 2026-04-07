# 05 — Ops, Latency, and Cost Checklist

## Latency tuning (realtime)
- Prefer `pcm_16000` mono input.
- Send chunk sizes around 100–500ms for low latency.
- Translate committed segments, not every partial.
- Keep translation prompts small; pass only recent context window.

## Accuracy tuning
- Set `language_code` when known; otherwise enable language detection.
- For domain terms, maintain glossary/term lock in translator layer.
- If available in batch mode, use keyterms and diarization where useful.
- Run periodic regression set with representative accents/noise.

## Reliability
- Handle websocket errors and auth/quota events explicitly.
- Reconnect with exponential backoff.
- On reconnect, provide short `previous_text` context on first send.
- Persist segment offsets/checkpoints for resume after failure.

## Security
- Use server-side API key whenever possible.
- For browser/client capture, mint short-lived single-use tokens (`realtime_scribe`).
- Do not expose long-lived ElevenLabs API keys in frontend apps.

## Cost control
- Gate realtime sessions with inactivity timeout.
- Cap max session length and max concurrent sessions per user/workspace.
- For long files, route to batch pipeline.
- Track:
  - STT minutes consumed
  - translation tokens consumed
  - optional TTS minutes/characters

## Suggested migration plan from `gpt-4o-transcribe`
1. Shadow mode: run ElevenLabs realtime STT alongside existing pipeline for comparison.
2. Compare metrics (latency, transcript quality, language robustness).
3. Switch 10% traffic with rollback switch.
4. Roll to 100% once error and quality thresholds stabilize.

## Practical verdict
For your current requirement (simultaneous transcript/translation):
- Build around **`scribe_v2_realtime` + your own translation layer**.
- Keep Dubbing API for separate localization workflows.
