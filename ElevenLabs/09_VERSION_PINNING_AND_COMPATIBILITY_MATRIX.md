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

From upstream commit `78cc4c1` (2026-08-13):

| Layer | Package | Version | Key changes since last snapshot |
|---|---|---|---|
| Core SDK | `@elevenlabs/client` | 1.17.0 | Self-hosted orchestrator sessions (experimental); `onRichContent` callback (experimental); Scribe `workletPaths.scribeAudioProcessor`; disconnect state consistency fix; `enableLogging` for Scribe zero retention; `onAgentReasoningResponsePart` (experimental); `onAudioAlignment` WebRTC fix; `onPing` callback; `sendFeedback` per-message targeting + null-clear; `overrides.asr.keywords`; `includeLanguageDetection`; Scribe mic reliability; defensive error handling; Unity bridge; platform restructure; `uploadFile` from Unity |
| React wrapper | `@elevenlabs/react` | 1.12.0 | `ConversationProvider` state consistency through disconnect; `enableLogging` prop on `useScribe`; `onAgentReasoningResponsePart`; `onPing`; `sendFeedback` null + eventId |
| React Native | `@elevenlabs/react-native` | 1.2.18 | All client improvements propagated; Metro package exports caveat for RN < 0.79 |
| Types | `@elevenlabs/types` | 0.19.0 | `RichContent` / `RichContentClientEvent` types (experimental); `enable_logging` renamed from `disable_logging`; reasoning response types; ping null values; feedback types; ASR keywords; full tool payload; manual types moved to client |
| Widget core | `@elevenlabs/convai-widget-core` | 0.15.1 | Markdown rendering fix for voice transcripts; `show_resize_button` config; file upload (`UploadFileButton`, `useFileUpload`); `EventBridge`; rich content callback; dynamic variables in first message; re-enabled text streaming in voice sessions; markdown for guardrail-blocked responses; external agent + typing indicators |
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
- If using orchestrator sessions, verify WebSocket-only constraint and webhook configuration
- Check `overrides.asr.keywords` format if using per-conversation ASR biasing
- Verify `sendFeedback(null, eventId)` clearing works end-to-end
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
- `78cc4c1bc059d25559db3f059ea8e350ff8b62f0` (2026-08-13)

Previous tracked commit:
- `da7b5323e0e0f5b3c7c0e8e8a0a1b2c3d4e5f6a7` (2026-08-10)

Observed surface changes in this revision (9 commits, 53 files changed):

**New experimental features:**
- Self-hosted orchestrator sessions — `OrchestratorSessionConfig` / `OrchestratorConfig` / `PostCallWebhookConfig` types; routes WebSocket to private deployment; sends `enclave_setup_config` on connect; supports post-call transcription/audio webhooks; Bedrock inference profile selection
- `onRichContent` callback — agent-sent UI components (`{ rich_content_id, component, props, event_id }`); widget-only currently; `RichContentClientEvent` type added to `@elevenlabs/types`
- Scribe `workletPaths.scribeAudioProcessor` — self-host audio worklet for strict CSP; worklets published as static assets under `@elevenlabs/client/worklets/*`

**Bug fixes:**
- Disconnect state consistency — `BaseConversation` always reaches `disconnected` + fires `onDisconnect` even if teardown throws; `ConversationProvider` prevents stale session callbacks from clobbering newer session state; `endSession()` rejections caught
- Widget markdown rendering — voice transcripts now render markdown (bold, lists, tables, links) instead of showing literal syntax; audio tags processed via rehype plugin after sanitization
- `ConversationProvider` `useLayoutEffect` removed; replaced with stale-session guard wrapping all callbacks

**Dependency upgrades:**
- LiveKit dependencies upgraded (removed `livekit-client@2.16.1.patch`)
- Turborepo upgraded to 2.10.8
- React group deps bumped (8 packages)

**Documentation:**
- React Native README: Metro package exports caveat for RN < 0.79
- Client README: expanded documentation

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
