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
| Core SDK | `@elevenlabs/client` | `1.1.2` | **MAJOR UPDATE**: v1.0+ breaking changes; enhanced WebSocket events |
| React wrapper | `@elevenlabs/react` | `1.0.3` | **MAJOR UPDATE**: Completely restructured components; new conversation context |
| React Native wrapper | `@elevenlabs/react-native` | `1.0.3` | **MAJOR UPDATE**: Modular imports; provider pattern removed |
| Types | `@elevenlabs/types` | `0.9.1` | Enhanced with type discriminants; new WebSocket message types |
| Widget core | `@elevenlabs/convai-widget-core` | `0.11.2` | Auto-language selection; enhanced tool mocking support |
| Widget embed | `@elevenlabs/convai-widget-embed` | `0.11.2` | Keep in sync with core |

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
- `46fed15736cf33313c2572cbdf6ca104446af66c`

Observed surface changes in this revision:
- **MAJOR UPDATE**: SDK v1.0+ breaking changes (see migration guide)
- **NEW**: Tool mocking support for agent development
- **NEW**: Auto-select widget language from browser locale and localStorage
- **NEW**: Enhanced guardrail events with `guardrail_triggered`
- **NEW**: Modular React Native SDK structure
- **NEW**: Enhanced React conversation context management
- **NEW**: WebSocket message types for multimodal content
- **UPDATED**: ESLint configs migrated to ESM format (.mjs)
- **UPDATED**: Improved migration tools and patterns for v1.0 upgrade

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