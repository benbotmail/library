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
| Core SDK | `@elevenlabs/client` | `0.15.1` | Adds multimodal message event plumbing |
| React wrapper | `@elevenlabs/react` | `0.14.2` | Exposes `sendMultimodalMessage` in hook surface |
| React Native wrapper | `@elevenlabs/react-native` | monorepo local package | Includes provider-side multimodal wiring |
| Types | `@elevenlabs/types` | monorepo local package | AsyncAPI + generated outgoing types now include `multimodal_message` |
| Widget core | `@elevenlabs/convai-widget-core` | `0.10.3` | Current embed baseline |
| Widget embed | `@elevenlabs/convai-widget-embed` | `0.10.3` | Keep in sync with core |

> For app-level production pinning, still lock exact versions in your own `package.json` + lockfile.

---

## 3) Runtime compatibility checks

At each upgrade:
- verify conversation auth flows still match (`signed-url`, `conversation-token`)
- verify Scribe token flow unchanged (`single-use-token/realtime_scribe`)
- replay reconnect tests and token-expiry tests
- verify event names/shape used by your handlers
- if using multimodal turns, validate both text-only and text+file payload paths

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
- `a00f0a4d276c9601e232794dae04987c6c0fabfd`

Observed surface change in this revision:
- new outbound socket event: `multimodal_message`
- new client method: `sendMultimodalMessage`
- shared resampler module loading for input/output audio worklets

Policy:
- when event or session surface changes, update `02_CONVERSATION_SESSION_PATTERNS.md` first
- then refresh this matrix so version/package expectations remain explicit

---

## 6) Upgrade runbook (short)

1. Bump pins in a dedicated branch
2. Rebuild + run smoke tests
3. Execute realtime QA suite (latency + reliability + quality)
4. Compare metrics vs prior baseline
5. Promote only if thresholds remain within accepted bounds

This keeps upgrades explicit, reversible, and measurable.
