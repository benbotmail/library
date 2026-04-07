# 01 — Capabilities and Limits (Important)

## TL;DR
ElevenLabs gives you:
- **Excellent speech-to-text** (batch + realtime) via `scribe_v2` / `scribe_v2_realtime`
- **Dubbing translation pipeline** (audio/video translation with voice preservation)

But for "simultaneous transcription + translation" in the style of live interpreted text:
- There is **no single realtime STT endpoint that directly outputs translated text**.
- The practical architecture is: **Realtime STT → Translation layer (LLM/NMT) → (optional) TTS**.

---

## What STT provides directly
From ElevenLabs docs:
- Realtime WebSocket STT with low latency (~150ms class) using `scribe_v2_realtime`
- Partial + committed transcript event stream
- Optional word-level timestamps (`include_timestamps=true`)
- Commit strategies: manual or VAD
- Language detection option (`include_language_detection`)
- Batch transcription endpoint with rich controls:
  - diarization
  - timestamps granularity
  - keyterms
  - entity detection
  - webhook async mode

## What translation provides directly
- ElevenLabs **Dubbing API** translates/dubs long-form audio/video (batch workflow).
- Great for localization and media delivery.
- Not the same as interactive, low-latency, line-by-line live translation feed.

---

## Decision rule for your use case
If you need voice-note / live stream translation in near real time:
1. Use `scribe_v2_realtime` for transcript stream
2. Translate transcript chunks yourself (LLM/NMT)
3. Optionally synthesize translated speech

If you need polished translated output for long media files:
- Use Dubbing API (`/v1/dubbing`) and retrieve output when done.

---

## Model IDs to anchor on
- `scribe_v2` (batch STT)
- `scribe_v2_realtime` (WebSocket STT)
- Dubbing endpoints are separate from STT models
