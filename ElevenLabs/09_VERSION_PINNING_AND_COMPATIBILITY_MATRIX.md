# 09 — Version Pinning and Compatibility Matrix

This appendix defines practical pinning policy so builds remain reproducible while upstream evolves.

## 1) Pinning policy (recommended)

Use two lock levels:

1. **Production lock (strict)**
   - Pin exact versions in lockfile
   - Upgrades only via scheduled refresh PRs

2. **Exploration lock (minor-safe)**
   - Allow minor updates in a feature branch
   - Validate with the QA gates in `07_EVALUATION_AND_ACCEPTANCE.md` before promotion

---

## 2) Current upstream package snapshot

From upstream commit `fc2380cf` (2026-08-17):

| Layer | Package | Version | Key changes since last snapshot |
|---|---|---|---|
| Core SDK | `@elevenlabs/client` | 1.20.0 | **1.20.0**: `webRtc.iceTransportPolicy` session option (TURN-only relay for UDP-blocked networks); Scribe `secondaryLanguages`, `entityDetection` (+ `committed_transcript_entities` event), `filterBackgroundAudio`; Scribe now dispatches `final_transcript`, `final_transcript_with_timestamps`, `invalid_request`; shared `Word` shape with `audio_event`/`channel_index`/per-character timings; `workletPaths` honored on WebRTC + cache keyed by source; RN setup-guard error points at `@elevenlabs/react-native`; scribe mic-permission failure is retriable. **1.18.0**: orchestrator sessions (experimental); `onRichContent`; Scribe `workletPaths.scribeAudioProcessor`; disconnect state consistency. On `main` (unreleased): `onIncomingEvent` / `onOutgoingEvent` monitoring callbacks |
| React wrapper | `@elevenlabs/react` | 1.12.4 | `WordTimestamp` widened to mirror client `Word` (+ `WordTimestampCharacter`); `useScribe` mic-permission retry + stale-close race fix; disconnect state consistency (1.12.1); `enableLogging` on `useScribe`; `onPing`; `sendFeedback` null + eventId |
| React Native | `@elevenlabs/react-native` | 1.2.22 | Client 1.20.0 propagation; Metro package exports caveat for RN < 0.79 |
| Types | `@elevenlabs/types` | 0.21.0 | Scribe asyncapi schema aligned with live contract: merged `Word` type, entity detection schemas, `final_transcript*` messages, `timestamps_granularity` / `max_tokens_to_recompute` config; earlier: `RichContent` types (0.20.0), `enable_logging` rename |
| Widget core | `@elevenlabs/convai-widget-core` | 0.16.3 | 0.16.x: self-hosted orchestrator via `orchestrator-url` + `orchestrator-agent-config` (experimental); rich content rendering in transcript (`buttons` quick replies, zod-mini validation, graceful fallback); language dropdown fix inside container-query ancestors; markdown voice-transcript fix |
| Widget embed | `@elevenlabs/convai-widget-embed` | 0.16.3 | Synced with core |

> For app-level production pinning, still lock exact versions in your own `package.json` + lockfile.

---

## 3) Runtime compatibility checks

At each upgrade:
- Verify conversation auth flows still match (`signed-url`, `conversation-token`)
- Verify Scribe token flow unchanged (`single-use-token/realtime_scribe`)
- Replay reconnect tests and token-expiry tests
- Verify event names/shapes used by your handlers — **especially new callbacks**
- If using multimodal turns, validate both text-only and text+file payload paths
- If using orchestrator sessions, verify WebSocket-only constraint and webhook configuration
- Check `overrides.asr.keywords` format if using per-conversation ASR biasing
- Verify `sendFeedback(null, eventId)` clearing works end-to-end
- If on restrictive networks, test `webRtc.iceTransportPolicy: "relay"` TURN-only sessions
- If using Scribe entity detection, validate `committed_transcript_entities` payload shapes
- If using Scribe timestamps, verify consumers handle the widened `Word` shape (`audio_event`, structured characters, `channel_index`)
- Test `onPing` callback fires with expected latency values
- Test `onRichContent` callback if rendering agent components (experimental)
- Verify `onAudioAlignment` fires on WebRTC transport (was broken before v1.15.2)
- If using Scribe microphone mode, test teardown/reconnect scenarios
- If using Scribe with strict CSP, verify `workletPaths.scribeAudioProcessor` loads correctly
- Verify widget `show_resize_button` config renders correctly
- Test file upload flow in widget if enabled
- Validate `enableLogging: false` behavior for Scribe (enterprise)

If any of the above changes, classify as "integration-impacting" and block promotion until patched.

---

## 4) Node/JS runtime guardrail

Define and enforce runtime bounds in project config:
- Set `engines.node` in `package.json`
- Enforce via CI (`npm ci` + smoke run)
- Keep one known-good LTS for production

---

## 5) Upstream freshness marker

Current tracked upstream commit in this docs pack:
- `fc2380cf2d964f3c1d55b24f53eba4cca680b2da` (2026-08-17)

Previous tracked commit:
- `6fabb8973605ea1d959fb59e16d8ccbb58ab71ff` (2026-08-16)

Observed surface changes in this revision (10 commits — client 1.18.0 → 1.20.0 releases):

**New session option:**
- `webRtc.iceTransportPolicy` (`"all"` | `"relay"`, default `"all"`) — pass `"relay"` for TURN-only connections on networks that drop direct UDP

**Scribe new options (client 1.20.0):**
- `secondaryLanguages: string[]` — restrict language-identification candidates
- `entityDetection` — category (`all`/`pii`/`phi`/`pci`/`other`/`offensive_language`), specific type (`email_address`, `credit_card`, …), or list; entities delivered via `committed_transcript_entities`
- `filterBackgroundAudio: boolean` — lowers default VAD threshold; **incompatible with `includeTimestamps`**

**Scribe new dispatched events:** `final_transcript`, `final_transcript_with_timestamps`, `committed_transcript_entities`, `invalid_request`

**Scribe behavior corrections:**
- `vadSilenceThresholdSecs` (0.3–3.0), `minSpeechDurationMs` (50–2000), `minSilenceDurationMs` (50–2000) lower bounds are now **inclusive**, matching the API
- Shared `Word` timestamp shape per live AsyncAPI contract: `audio_event` type, structured `TranscriptCharacter[]`, `channel_index`; react `WordTimestamp` mirrors it
- Mic-permission failure closes the connection (retriable without remount); `useScribe` ignores stale close from superseded connection

**Fixes:**
- `workletPaths` now passed through on the WebRTC connection path (output capture honors self-hosted worklets under strict CSP)
- Worklet module cache keyed by requested source — no more stale `blob:` URL served for self-hosted paths
- React Native setup error message points at `@elevenlabs/react-native`

Policy:
- When event or session surface changes, update `02_CONVERSATION_SESSION_PATTERNS.md` first
- Then refresh this matrix so version/package expectations remain explicit

---

## 6) Upgrade runbook (short)

1. Bump pins in a dedicated branch
2. Review migration guide for breaking changes
3. Rebuild + run smoke tests
4. Execute realtime QA suite (latency + reliability + quality)
5. Compare metrics vs prior baseline
6. Validate new callbacks (`onPing`, `onAgentReasoningResponsePart`, `onRichContent`) if wiring them
7. Test `sendFeedback` null-clearing and per-message targeting
8. Verify `onAudioAlignment` fires on WebRTC if using alignment
9. Test Scribe microphone teardown scenarios
10. If using orchestrator sessions, verify WebSocket-only + webhook setup
11. If using strict CSP, test Scribe `workletPaths` self-hosting
12. Promote only if thresholds remain within accepted bounds
