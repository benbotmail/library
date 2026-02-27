# ElevenLabs Docs Pack (Current-State)

This pack documents the **current behavior** of `elevenlabs/packages` for production implementers.

- Upstream repo: `github.com/elevenlabs/packages`
- Upstream commit: `f61e7282529a92f7f3332cf1cb3d8fe2fe480df4`
- Focus: what is available **now**, and how to use it safely

## Package Surface (current)

1. `@elevenlabs/client` — core browser/client SDK for agent conversations (WebRTC/WebSocket) and Scribe realtime STT.
2. `@elevenlabs/react` — React hook wrapper for conversation lifecycle.
3. `@elevenlabs/react-native` — React Native SDK (LiveKit dependencies required).
4. `@elevenlabs/convai-widget-core` — embeddable widget core package.
5. `@elevenlabs/convai-widget-embed` — pre-bundled widget embed package.
6. `@elevenlabs/types` — generated message/event TypeScript types from AsyncAPI specs.

> Important release shift: this upstream revision removes `packages/agents-cli` from the monorepo. Do not rely on prior CLI workflows from older snapshots.

## Reading Order

- Start with `01_CURRENT_PRODUCT_SCOPE.md`
- Then `02_WEB_CONVERSATION_PATTERNS.md`
- Use `03_SCRIBE_REALTIME_STT_PATTERNS.md` if you need low-latency transcripts/subtitles
- Use `04_REACT_NATIVE_IMPLEMENTATION_NOTES.md` for mobile
- Keep `05_SECURITY_AND_DEPLOYMENT_CHECKLIST.md` open during production hardening
