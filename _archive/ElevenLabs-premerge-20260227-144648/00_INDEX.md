# ElevenLabs Simultaneous Transcription/Translation Docs Pack

This pack is focused on building a **near-real-time transcription + translation pipeline** with ElevenLabs APIs, as a migration alternative to `gpt-4o-transcribe` workflows.

## Read order
1. `01_CAPABILITIES_AND_LIMITS.md`
2. `02_ARCHITECTURE_PATTERNS.md`
3. `03_IMPLEMENTATION_BLUEPRINT_NODE.md`
4. `04_BATCH_TRANSLATION_WITH_DUBBING.md`
5. `05_OPS_LATENCY_COST_CHECKLIST.md`

## Key source docs
- STT overview: https://elevenlabs.io/docs/overview/capabilities/speech-to-text
- Batch STT endpoint: https://elevenlabs.io/docs/api-reference/speech-to-text/convert
- Realtime STT endpoint (WebSocket): https://elevenlabs.io/docs/api-reference/speech-to-text/v-1-speech-to-text-realtime
- Realtime commit strategies: https://elevenlabs.io/docs/eleven-api/guides/cookbooks/speech-to-text/realtime/transcripts-and-commit-strategies
- Models list: https://elevenlabs.io/docs/overview/models
- Token endpoint for client-safe realtime auth: https://elevenlabs.io/docs/api-reference/tokens/create
- Dubbing capability overview: https://elevenlabs.io/docs/overview/capabilities/dubbing
- Dubbing create/status/audio: 
  - https://elevenlabs.io/docs/api-reference/dubbing/create
  - https://elevenlabs.io/docs/api-reference/dubbing/get
  - https://elevenlabs.io/docs/api-reference/dubbing/audio/get
