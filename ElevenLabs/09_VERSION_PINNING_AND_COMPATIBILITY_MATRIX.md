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

From upstream commit `6fabb89` (2026-08-16):

| Layer | Package | Version | Key changes since last snapshot |
|---|---|---|---|
| Core SDK | `@elevenlabs/client` | 1.18.0 | **Released in 1.18.0** (previously documented from source as v1.17.0): self-hosted orchestrator sessions (experimental); `onRichContent` callback (experimental); Scribe `workletPaths.scribeAudioProcessor`; disconnect state consistency fix. On `main` (unreleased): `onIncomingEvent` / `onOutgoingEvent` raw connection monitoring callbacks. Earlier: `enableLogging` zero retention; `onAgentReasoningResponsePart`; `onAudioAlignment` WebRTC fix; `onPing`; `sendFeedback` targeting; `overrides.asr.keywords`; Unity bridge |
| React wrapper | `@elevenlabs/react` | 1.12.1 | Disconnect state consistency (v1.12.1); `onIncomingEvent`/`onOutgoingEvent` in `HookCallbacks` (unreleased on main); `enableLogging` on `useScribe`; `onPing`; `sendFeedback` null + eventId |
| React Native | `@elevenlabs/react-native` | 1.2.19 | Client 1.18.0 propagation; Metro package exports caveat for RN < 0.79 |
| Types | `@elevenlabs/types` | 0.20.0 | `RichContent` / `RichContentClientEvent` types (experimental) — released in 0.20.0; earlier: `enable_logging` rename; reasoning/ping/feedback/ASR-keyword types |
| Widget core | `@elevenlabs/convai-widget-core` | 0.16.0 | **0.16.0**: self-hosted orchestrator via `orchestrator-url` + `orchestrator-agent-config` attributes (experimental); rich content rendering in transcript (`buttons` quick replies, zod-mini validation, graceful fallback); language dropdown fix inside CSS container-query ancestors; markdown voice-transcript fix (released from source state) |
| Widget embed | `@elevenlabs/convai-widget-embed` | 0.16.0 | Synced with core |

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
- `6fabb8973605ea1d959fb59e16d8ccbb58ab71ff` (2026-08-16)

Previous tracked commit:
- `78cc4c1bc059d25559db3f059ea8e350ff8b62f0` (2026-08-13)

Observed surface changes in this revision (5 commits, 50 files changed — mostly the "Version Packages" release):

**Releases (previously documented from source, now official):**
- `@elevenlabs/client` 1.18.0 — orchestrator sessions, `onRichContent`, Scribe `workletPaths`, disconnect fixes
- `@elevenlabs/types` 0.20.0 — rich content event types
- `@elevenlabs/react` 1.12.1 — disconnect state consistency
- `@elevenlabs/react-native` 1.2.19 — dependency propagation

**New since 78cc4c1:**
- Widget self-hosted orchestrator — `orchestrator-url` + `orchestrator-agent-config` attributes (widget-core 0.16.0, experimental); `orchestrator-url` overrides `agent-id`/`signed-url`; http(s) auto-upgraded to ws(s); agent config JSON snake_case keys parsed into `OrchestratorConfig`; invalid config dropped with console error
- Widget rich content rendering — `buttons` quick-reply component rendered inline in transcript; `message` buttons send user turns, `link` buttons open https URLs; max 3 buttons, labels capped at 500 chars; zod-mini validation (~5 KB gz); fallback notice for malformed/unrecognized components
- `onIncomingEvent` / `onOutgoingEvent` — raw socket monitoring callbacks in client `Callbacks` + `CALLBACK_KEYS` and react `HookCallbacks` (**on main, unreleased**); outgoing events queued until handler attaches

**Bug fixes:**
- Widget language dropdown positioned wrong when embedded inside a `container-type: inline-size` (container-query) ancestor — overlay root now establishes its own containing block

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
