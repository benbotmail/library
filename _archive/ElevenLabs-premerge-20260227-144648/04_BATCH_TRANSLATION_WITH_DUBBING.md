# 04 — Batch Translation with Dubbing API

Use this when your output artifact is **translated audio/video**, not just translated text.

## Endpoint workflow

1. Create dubbing job
- `POST https://api.elevenlabs.io/v1/dubbing`
- multipart form supports `file` upload or `source_url`
- choose `source_lang` and `target_lang`

2. Poll job metadata
- `GET https://api.elevenlabs.io/v1/dubbing/{dubbing_id}`
- inspect `status`, `error`, metadata

3. Download dubbed output
- `GET https://api.elevenlabs.io/v1/dubbing/{dubbing_id}/audio/{language_code}`
- returns streamed media (MP3/MP4)

---

## Curl template

```bash
# 1) Create job
curl -sS -X POST "https://api.elevenlabs.io/v1/dubbing" \
  -H "xi-api-key: $ELEVENLABS_API_KEY" \
  -F "file=@/path/input.mp4" \
  -F "name=my-dub-job" \
  -F "source_lang=auto" \
  -F "target_lang=es"

# -> parse dubbing_id from JSON

# 2) Poll status
curl -sS "https://api.elevenlabs.io/v1/dubbing/$DUBBING_ID" \
  -H "xi-api-key: $ELEVENLABS_API_KEY"

# 3) Download result
curl -L "https://api.elevenlabs.io/v1/dubbing/$DUBBING_ID/audio/es" \
  -H "xi-api-key: $ELEVENLABS_API_KEY" \
  -o dubbed-es.mp4
```

---

## When to pick dubbing vs STT+translator

Pick dubbing when:
- you need translated **voice media** output
- speaker identity/emotion preservation matters
- asynchronous pipeline is acceptable

Pick STT+translator when:
- you need realtime translated **text stream**
- you want flexible custom business logic per segment
- you are integrating with chat/agent UX and low latency matters
