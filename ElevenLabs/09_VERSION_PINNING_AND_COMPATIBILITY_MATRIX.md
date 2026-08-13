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

## 2) Package matrix template (fill per project)

| Layer | Package | Pin strategy | Current pin (example) | Notes |
|---|---|---|---|---|
| Core SDK | `@elevenlabs/client` | exact | `x.y.z` | Primary runtime surface |
| React wrapper | `@elevenlabs/react` | exact | `x.y.z` | Keep aligned with client major |
| React Native wrapper | `@elevenlabs/react-native` | exact | `x.y.z` | Validate audio path behavior on target devices |
| Types | `@elevenlabs/types` | exact | `x.y.z` | Shared contracts across services |
| Widget core | `@elevenlabs/convai-widget-core` | exact | `x.y.z` | Optional, only if using embedded widgets |
| Widget embed | `@elevenlabs/convai-widget-embed` | exact | `x.y.z` | Optional, browser embedding |

> Keep these pins in `package.json` + lockfile, and mirror the same values in release notes.

---

## 3) Runtime compatibility checks

At each upgrade:
- verify conversation auth flows still match (`signed-url`, `conversation-token`)
- verify Scribe token flow unchanged (`single-use-token/realtime_scribe`)
- replay reconnect tests and token-expiry tests
- verify event names/shape used by your handlers

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

## 5) Upstream freshness and removed-package policy

Current tracked upstream commit in this docs pack:
- `f61e7282529a92f7f3332cf1cb3d8fe2fe480df4`

Observed package-surface change in this revision:
- `packages/agents-cli` is absent from the monorepo surface.

Policy:
- if a previously referenced package is removed/renamed upstream,
  - remove it from decision matrices
  - add a migration note in `01_PRODUCT_SURFACE_AND_SCOPE.md`
  - record the commit hash where the change was observed

---

## 6) Upgrade runbook (short)

1. Bump pins in a dedicated branch
2. Rebuild + run smoke tests
3. Execute realtime QA suite (latency + reliability + quality)
4. Compare metrics vs prior baseline
5. Promote only if thresholds remain within accepted bounds

This keeps upgrades explicit, reversible, and measurable.