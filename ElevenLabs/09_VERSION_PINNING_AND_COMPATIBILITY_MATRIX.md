# 09 — Version Pinning and Compatibility Matrix

This appendix defines practical pinning policy so builds remain reproducible while upstream evolves.

## 1) Pinning policy (recommended)

Use two lock levels:

1. **Production lock (strict)**
   - pin exact versions in lockfile
   - upgrades only via scheduled refresh PRs

2. **Exploration lock (minor-safe)**
   - allow minor updates in a feature branch
   - validate with the QA gates in `07_EVALUATION_AND_ACCEPTANCE.md` before promotion

---

## 2) Current upstream package snapshot (from tracked commit)

| Layer | Package | Current version seen upstream | Notes |
|---|---|---|---|
| Core SDK | `@elevenlabs/client` | `1.3.0` | `uploadFile()` for conversation file uploads; `MultimodalMessageInput`/`UploadFileResult` types; `SessionConnectionError` exported |
| React wrapper | `@elevenlabs/react` | `1.2.0` | `uploadFile` in `useConversationControls`; `onConnect` lifecycle fix |
| React Native wrapper | `@elevenlabs/react-native` | `1.1.2` | Upload API; `ImageUpload` example |
| Types | `@elevenlabs/types` | `0.9.1` | Enhanced with type discriminants; new WebSocket message types |
| Widget core | `@elevenlabs/convai-widget-core` | `0.11.4` | Keep in sync |
| Widget embed | `@elevenlabs/convai-widget-embed` | `0.11.4` | Keep in sync with core |

> For app-level production pinning, still lock exact versions in your own `package.json` + lockfile.

---

## 3) Runtime compatibility checks

At each upgrade:
- verify conversation auth flows still match (`signed-url`, `conversation-token`)
- verify Scribe token flow unchanged (`single-use-token/realtime_scribe`)
- replay reconnect tests and token-expiry tests
- verify event names/shape used by your handlers
- if using multimodal turns, validate both text-only and text+file payload paths
- **NEW**: check tool mocking configuration and agent response patterns
- **NEW**: verify auto-language selection from browser locale works as expected
- **NEW**: validate `guardrail_triggered` event handling for compliance monitoring

If any of the above changes, classify as "integration-impacting" and block promotion until patched.

---

## 4) Node/JS runtime guardrail

Define and enforce runtime bounds in project config:
- set `engines.node` in `package.json`
- enforce via CI (`npm ci` + smoke run)
- keep one known-good LTS for production

Recommended practice:
- maintain a `docs/runtime-matrix.md` in your app repo with:
  - Node version
  - package manager version
  - OS images used in CI and production

---

## 5) Upstream freshness marker

Current tracked upstream commit in this docs pack:
- `7afc0e5fb8c93ea5be52cac0fba7ee1c0cb4c0de`

Observed surface changes in this revision:
- **v1.3.0 client**: New `uploadFile()` method on `BaseConversation` for uploading files to a conversation; `MultimodalMessageInput` and `UploadFileResult` types; `SessionConnectionError` class exported from errors module; `extractApiErrorMessage` utility for parsing upload error responses; `sendMultimodalMessage` updated to accept `fileId`
- **v1.2.0 react**: `uploadFile` exposed through `ConversationControls`; `onConnect` lifecycle fix ensures `conversationRef` is set before callback fires; improved error handling in `ConversationControlsProvider`
- **v1.1.2 react-native**: Upload API exposed via `useConversationControls`; new `ImageUpload` example component demonstrating image picker + upload + multimodal send flow
- **v0.11.5 widget-core/embed**: Version bump

Policy:
- when event or session surface changes, update `02_CONVERSATION_SESSION_PATTERNS.md` first
- then refresh this matrix so version/package expectations remain explicit
- **CRITICAL**: For major version changes (v1.0+), mandatory review of migration guide

---

## 6) Upgrade runbook (short)

1. Bump pins in a dedicated branch
2. Review migration guide for breaking changes
3. Rebuild + run smoke tests
4. Execute realtime QA suite (latency + reliability + quality)
5. Compare metrics vs prior baseline
6. **NEW**: Validate tool mocking behavior
7. **NEW**: Test auto-language selection functionality
8. Promote only if thresholds remain within accepted bounds

This keeps upgrades explicit, reversible, and measurable.

---

## 7) Migration warnings

⚠️ **v0.x to v1.0 MIGRATION REQUIRED**
- React Native SDK: Switch from ElevenLabsProvider to modular imports
- Client SDK: Enhanced WebSocket event structure
- Types: Updated discrimination patterns
- Breaking changes in conversation state management

⚠️ **Action Required**: Follow migration guide in `10_SDK_MIGRATION_V1_PREVIEW.md` before upgrading to v1.0+ packages.