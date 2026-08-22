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

From upstream commit `2a9057ee` (2026-08-22):

| Layer | Package | Version | Key changes since last snapshot |
|---|---|---|---|
| Core SDK | `@elevenlabs/client` | 1.21.0 | **1.21.0**: `onContextUsage` callback — `context_usage` server event after each completed agent turn (`{ event_id, model, context_tokens, context_limit_tokens }`); React Native resolved via `react-native` export condition instead of global sniffing; platform entry points inject setup guidance (RN error says to `import "@elevenlabs/react-native"` before other imports). **On `main` (unreleased)**: `onMCPToolApprovalRequest` MCP approval handler (SDK wires `mcp_tool_approval_result`, exactly-once per `tool_call_id`, AbortSignal for stale windows); `onExternalAgentDisconnected` callback; WebRTC setup hard-fails when initiation payload can't be published (no more ghost connected sessions). Earlier 1.20.0: `webRtc.iceTransportPolicy`, Scribe `secondaryLanguages`/`entityDetection`/`filterBackgroundAudio`, `final_transcript*` events, widened `Word` shape, `workletPaths` on WebRTC. `onIncomingEvent`/`onOutgoingEvent` present in released code since 1.20.0 |
| React wrapper | `@elevenlabs/react` | 1.13.0 | `onContextUsage` added to `HookCallbacks`; client 1.21.0 propagation. On main (unreleased): `onExternalAgentDisconnected` in `HookCallbacks` |
| React Native | `@elevenlabs/react-native` | 1.2.23 | Client 1.21.0 propagation; Expo example app upgraded to SDK 57, `expo-modules-jsi` local patch dropped in favor of 57.0.5 |
| Types | `@elevenlabs/types` | 0.21.1 | `ContextUsage`/`ContextUsageEvent` types; `context_usage`, `external_agent_connected`/`external_agent_disconnected` in agent asyncapi schema + generated types. On main (unreleased): MCP approval shapes |
| Widget core | `@elevenlabs/convai-widget-core` | 0.16.4 | 0.16.4: client 1.21.0 dependency bump. **On `main` (unreleased, next minor)**: concurrency wait-queue UX (`queue_status` events — waiting status, blocked sends, `queue_timed_out` friendly message); `first_message_rich_content` (rich content painted with first message on cold start, button taps start conversation + `rich_content_id` attribution); text-mode first message for any text-capable agent (`strip_audio_tags` honored); `show_language_selector_on_trigger` config + attribute (default `true`) |
| Widget embed | `@elevenlabs/convai-widget-embed` | 0.16.4 | Synced with core |

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
- Wire `onContextUsage` and verify payload shape after agent turns (client 1.21.0)
- If wiring MCP approvals on a pre-release client, verify `onMCPToolApprovalRequest` resolves booleans only and abort-signal dismissal works
- If using the widget with concurrency limits, plan UX for wait-queue states (blocked sends, queue timeout message) once released
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
- `2a9057eec7d18709306e3831385fc6e300f18b41` (2026-08-22)

Previous tracked commit:
- `fc2380cf2d964f3c1d55b24f53eba4cca680b2da` (2026-08-17)

Observed surface changes in this revision (12 commits — client 1.20.0 → 1.21.0 releases + pending changesets):

**Released (client 1.21.0 / react 1.13.0 / types 0.21.1 / RN 1.2.23 / widget 0.16.4):**
- New `onContextUsage` callback — `context_usage` server event after each completed agent turn; reports model, `context_tokens` (last LLM generation's prompt size), and `context_limit_tokens` (model context window)
- React Native resolution via `react-native` export condition; platform setup hints injected from entry points (no more global runtime sniffing); actionable missing-setup error for RN

**On `main`, pending release (7 changesets):**
- `onMCPToolApprovalRequest` — opt-in MCP tool approval handler; SDK sends `mcp_tool_approval_result` exactly once per `tool_call_id`; non-boolean resolution/rejection → `onError` + denial; `AbortSignal` in context for stale approval windows; manual `sendMCPToolApprovalResult()` path unaffected
- `onExternalAgentDisconnected` — external-agent handback event (client, react, widget: exits external-agent mode and clears typing indicator)
- WebRTC: session setup now rejects when `conversation_initiation_client_data` can't be published (previously could fire `onConnect` for a session the server never initialized)
- Widget: concurrency wait-queue support (`queue_status` events, waiting status strings, blocked text/attachment sends, friendly queue-timeout); `first_message_rich_content` config (cold-start rich content + `rich_content_id` button-tap attribution via `sendUserMessage`); text-mode first message rendered for any text-capable agent (incl. `override-first-message`, `strip_audio_tags` honored); `show_language_selector_on_trigger` config/attribute (default `true`, hide dropdown on collapsed launcher)
- Examples: Expo SDK 57 upgrade, `expo-modules-jsi` local patch dropped (use 57.0.5+)

**Corrections to prior snapshot:**
- `onIncomingEvent` / `onOutgoingEvent` were listed as "main, unreleased" — they are present in released code since 1.20.0 (shipped without a dedicated changelog entry)

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
12. Verify `onContextUsage` wiring and MCP approval handler behavior before promoting past client 1.21.x
13. Promote only if thresholds remain within accepted bounds
