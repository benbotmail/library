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

From upstream commit `da7b532` (2026-08-10):

| Layer | Package | Version | Key changes since last snapshot |
|---|---|---|---|
| Core SDK | `@elevenlabs/client` | 1.17.0 | `enableLogging` for Scribe zero retention; `onAgentReasoningResponsePart` (experimental); `onAudioAlignment` WebRTC fix; `onPing` callback; `sendFeedback` per-message targeting + null-clear; `overrides.asr.keywords`; `includeLanguageDetection`; Scribe mic reliability; defensive error handling; Unity bridge; platform restructure; `uploadFile` from Unity |
| React wrapper | `@elevenlabs/react` | 1.12.0 | `enableLogging` prop on `useScribe`; `onAgentReasoningResponsePart`; `onPing`; `sendFeedback` null + eventId |
| React Native | `@elevenlabs/react-native` | 1.2.18 | All client improvements propagated |
| Types | `@elevenlabs/types` | 0.19.0 | `enable_logging` renamed from `disable_logging`; reasoning response types; ping null values; feedback types; ASR keywords; full tool payload; manual types moved to client |
| Widget core | `@elevenlabs/convai-widget-core` | 0.15.1 | `show_resize_button` config; file upload (`UploadFileButton`, `useFileUpload`); `EventBridge`; `TextWithAudioTags`; `ShimmeringText`; dynamic variables in first message; re-enabled text streaming in voice sessions; markdown for guardrail-blocked responses; external agent + typing indicators; transcript rendering fixes |
| Widget embed | `@elevenlabs/convai-widget-embed` | 0.15.1 | Synced with core |

> For app-level production pinning, still lock exact versions in your own `package.json` + lockfile.

---

## 3) Runtime compatibility checks

At each upgrade:
- Verify conversation auth flows still match (`signed-url`, `conversation-token`)
- Verify Scribe token flow unchanged (`single-use-token/realtime_scribe`)
- Replay reconnect tests and token-expiry tests
- Verify event names/shapes used by your handlers — **especially new callbacks**
- If using multimodal turns, validate both text-only and text+file payload paths
- Check `overrides.asr.keywords` format if using per-conversation ASR biasing
- Verify `sendFeedback(null, eventId)` clearing works end-to-end
- Test `onPing` callback fires with expected latency values
- Verify `onAudioAlignment` fires on WebRTC transport (was broken before v1.15.2)
- If using Scribe microphone mode, test teardown/reconnect scenarios
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
- `da7b5323e0e0f5b3c7c0e8e8a0a1b2c3d4e5f6a7` (2026-08-10)

Previous tracked commit:
- `7afc0e5fb8c93ea5be52cac0fba7ee1c0cb4c0de`

Observed surface changes in this revision (169 files changed across 40+ commits):

**New callbacks:**
- `onAgentReasoningResponsePart` — streaming agent reasoning (experimental)
- `onPing` — server ping events with latency estimate

**Changed behaviors:**
- `sendFeedback(like, eventId?)` — now targets past messages and supports null-clearing
- `canSendFeedback` — reflects connection state, not latest-turn-rated state
- `onAudioAlignment` — now works correctly on WebRTC transport
- Dynamic variables — now replaced in agent's first message
- Widget text streaming — re-enabled in voice sessions
- `handleErrorEvent` — defensively handles malformed error payloads

**New Scribe options:**
- `enableLogging` — zero retention mode (enterprise, replaces `disable_logging`)
- `includeLanguageDetection` — language detection metadata
- `keyterms` — model biasing (already documented, now fully integrated)
- `noVerbatim` — clean transcript output
- Microphone mode reliability improvements

**New conversation options:**
- `overrides.asr.keywords` — per-conversation ASR keyword biasing
- `toolMockConfig` — tool mocking configuration

**Widget features:**
- `show_resize_button` config option
- File upload support (`UploadFileButton`, `useFileUpload`, `PendingFilePreview`)
- `EventBridge` for widget event communication
- `TextWithAudioTags` component for audio tag rendering
- Dynamic variables in first message
- External agent typing indicators
- Transcript rendering fixes (tool call interleaving)

**Architecture changes:**
- Types relocation: `Role`, `Mode`, `Status`, `Callbacks`, etc. now in `@elevenlabs/client`
- Platform abstraction: `platform/web/` directory structure
- Runtime compatibility checks (`runtime.ts`)
- Unity WebGL bridge (`@elevenlabs/client/internal/unity`)

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
6. Validate new callbacks (`onPing`, `onAgentReasoningResponsePart`) if wiring them
7. Test `sendFeedback` null-clearing and per-message targeting
8. Verify `onAudioAlignment` fires on WebRTC if using alignment
9. Test Scribe microphone teardown scenarios
10. Promote only if thresholds remain within accepted bounds
