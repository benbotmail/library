# ElevenLabs Docs Deep Accuracy Audit (00–09)

**Audit target:** `/home/mamajjou/.openclaw/workspace/library/ElevenLabs/00_INDEX.md` … `09_VERSION_PINNING_AND_COMPATIBILITY_MATRIX.md`  
**Report date:** 2026-02-28 (UTC)  
**Local upstream validated against:** `/home/mamajjou/.openclaw/workspace/open-source/elevenlabs` @ `f61e7282529a92f7f3332cf1cb3d8fe2fe480df4`  

## Method used
1. Parsed claims in docs 00–09 (API paths/methods, auth flows, package surface, events/options, compatibility/policy claims).
2. Validated against local monorepo source and READMEs, especially:
   - `packages/client/README.md`
   - `packages/client/src/scribe/{scribe.ts,connection.ts}`
   - `packages/client/src/{index.ts,BaseConversation.ts,utils/BaseConnection.ts}`
   - `packages/{react,react-native,types,convai-widget-*}/package.json`
   - repo root `README.md`
3. Cross-checked selective public docs/web references (best-effort due some pages returning 404/403 via fetch):
   - `https://elevenlabs.io/docs/api-reference/speech-to-text/v-1-speech-to-text-realtime`
   - `https://elevenlabs.io/docs/api-reference/tokens/create`
   - Brave web search results for conversation signed-url/token docs and package/changelog pages.

---

## Critical inaccuracies (ranked)

### 1) **Medium severity** — `05_OPERATIONS_CHECKLIST.md` states `@elevenlabs/agents-cli` is “marked deprecated” in monorepo
- **Claim:** `05` line 31.
- **Finding:** **OUTDATED / INACCURATE wording**.
- **Why:** In this local upstream revision, `packages/agents-cli` is absent entirely (not present as a package with deprecation marker).
- **Evidence:**
  - Local package directories list excludes `agents-cli`: `open-source/elevenlabs/packages` contains `client`, `react`, `react-native`, `types`, `convai-widget-core`, `convai-widget-embed`, `typescript-config`.
  - `00_INDEX.md` and `09_...` correctly say “removed/absent”, which conflicts with `05` wording.
- **Impact:** Could mislead maintainers to search for a deprecated package in-repo instead of treating it as removed from this monorepo snapshot.

### 2) **Low/Medium severity** — `00_INDEX.md` canonical file pointers for widget READMEs are wrong in this revision
- **Claim:** `00` lines 30 references `packages/convai-widget-core/README.md` and `packages/convai-widget-embed/README.md`.
- **Finding:** **OUTDATED / UNVERIFIED for this repo checkout**.
- **Why:** Those README files do not exist in this local checkout (package exists, but no README at those paths).
- **Impact:** Breaks traceability for auditors/readers trying to verify widget claims.

---

## Per-file findings and confidence

| Doc | Confidence (0-100) | Safe to rely on for implementation? | Notes |
|---|---:|---|---|
| `00_INDEX.md` | 88 | **Yes, with minor caveat** | Accurate commit/scope, but widget README path references are stale. |
| `01_PRODUCT_SURFACE_AND_SCOPE.md` | 93 | **Yes** | Core package/auth/STT constraints align with upstream code/docs. |
| `02_CONVERSATION_SESSION_PATTERNS.md` | 95 | **Yes** | Session/auth/options/callback recommendations are aligned and practical. |
| `03_SCRIBE_REALTIME_STT_PATTERNS.md` | 96 | **Yes** | Strong alignment with Scribe code + README + API docs. |
| `04_MULTILINGUAL_SUBTITLE_ARCHITECTURE.md` | 80 | **Yes, as architecture guidance** | Mostly opinionated design guidance; not vendor-contract claims. |
| `05_OPERATIONS_CHECKLIST.md` | 85 | **Yes, with one correction** | `agents-cli` deprecation wording should be changed to “absent in this monorepo revision.” |
| `06_REFERENCE_CONFIGS_AND_PIPELINE.md` | 89 | **Yes, as implementation blueprint** | Mostly best-practice guidance; endpoint shapes are plausible and aligned. |
| `07_EVALUATION_AND_ACCEPTANCE.md` | 84 | **Yes, as QA policy** | Thresholds are opinionated, not vendor guarantees. |
| `08_API_ENDPOINTS_AND_ERROR_SURFACE_APPENDIX.md` | 94 | **Yes** | Endpoints/methods/events align with upstream client docs and public API references. |
| `09_VERSION_PINNING_AND_COMPATIBILITY_MATRIX.md` | 92 | **Yes** | Pinning and compatibility process is sound; mostly operational guidance. |

---

## Claim table (classification + evidence)

> Legend: **VERIFIED / PARTIALLY VERIFIED / UNVERIFIED / OUTDATED / OPINIONATED-BEST-PRACTICE**

### `00_INDEX.md`

| Claim | Class | Evidence |
|---|---|---|
| Upstream commit is `f61e7282529a92f7f3332cf1cb3d8fe2fe480df4` | VERIFIED | `git rev-parse HEAD` in local repo -> exact hash. |
| Pack scope includes `@elevenlabs/client`, Scribe STT, ops guidance | VERIFIED | `00` lines 8–11; matches package/client README scope and doc set. |
| Canonical source files include widget READMEs at listed paths | OUTDATED | `00` line 30 vs missing files under `packages/convai-widget-core/README.md` and `...-embed/README.md`. |
| `packages/agents-cli` removed in this revision | VERIFIED | `00` lines 33–34; directory listing of `/packages` has no `agents-cli`. |

### `01_PRODUCT_SURFACE_AND_SCOPE.md`

| Claim | Class | Evidence |
|---|---|---|
| `Conversation.startSession` is primary conversation entrypoint | VERIFIED | `packages/client/src/index.ts` lines 70–76; client README lines 66–70. |
| `Scribe.connect` used for realtime STT | VERIFIED | `packages/client/src/scribe/scribe.ts` class `ScribeRealtime.connect` lines 207–239; README Scribe examples lines 522–533. |
| Relevant packages list (`client/react/react-native/types/widget-*`) | VERIFIED | package.json names under each package directory. |
| Private conversation auth uses signed URL (WS) or conversation token (WebRTC) | VERIFIED | client README lines 74–75, 81–84, 119–122, 148–151; `BaseConnection.ts` union types lines 69–81. |
| Scribe uses server-minted single-use token; key must stay server-side | VERIFIED | client README lines 487–504, 513; token endpoint in public docs `/v1/single-use-token/{token_type}` (`realtime_scribe`). |
| WebRTC mode fixed `pcm_48000` behavior notes | VERIFIED | client README lines 412, 418, 438. |
| `previousText` only valid in first audio chunk | VERIFIED | client README lines 751–756; connection.ts lines 395–396. |
| Partial provisional vs committed authoritative for subtitles | OPINIONATED-BEST-PRACTICE | Consistent with event semantics in README lines 596–604 but “authoritative” policy is architectural guidance. |

### `02_CONVERSATION_SESSION_PATTERNS.md`

| Claim | Class | Evidence |
|---|---|---|
| Public start with `agentId` + `connectionType` | VERIFIED | client README lines 63–69. |
| Private recommended for production | OPINIONATED-BEST-PRACTICE | Architecture recommendation; auth mechanism itself verified by README. |
| Suggested callbacks (`onStatusChange`, `onError`, etc.) exist | VERIFIED | client README lines 154–177; callback types in `types/src/types.ts` lines 58–99. |
| Advanced callbacks (`onVadScore`, MCP, tool request/response) exist | VERIFIED | README lines 169–173; types callbacks lines 70–82. |
| Reliability preset values include `connectionDelay`, `preferHeadphonesForIosDevices`, `useWakeLock` | VERIFIED | README lines 271–305 and 281–295. |
| Android delay mitigates first-utterance clipping | VERIFIED | README lines 283–286. |
| `textOnly: true` avoids mic prompt/audio context | VERIFIED | README lines 261–264, 266–269. |
| Log conversation ID via `getId()` | VERIFIED | README lines 371–377. |
| Explicit `endSession()` to avoid dangling sessions | PARTIALLY VERIFIED | Method exists (`README` lines 313–320; BaseConversation lines 134–150). “dangling sessions” impact is operational interpretation. |

### `03_SCRIBE_REALTIME_STT_PATTERNS.md`

| Claim | Class | Evidence |
|---|---|---|
| Example config fields (`modelId`, `languageCode`, `includeTimestamps`, VAD params) are valid | VERIFIED | `scribe.ts` options lines 19–69, 118–174; README lines 645–661. |
| `AudioFormat.PCM_16000` and sampleRate pairing for manual mode | VERIFIED | README lines 544–549, 686–688; enum in `scribe.ts` lines 4–12. |
| Realtime events `PARTIAL_TRANSCRIPT`, `COMMITTED_*`, `ERROR` | VERIFIED | connection.ts `RealtimeEvents` lines 98–117; README lines 596–616. |
| `VAD` vs `MANUAL` strategy usage | VERIFIED | enum in `scribe.ts` lines 14–17; README lines 691–730. |
| `previousText` first-chunk-only constraint | VERIFIED | README lines 751–756; connection.ts lines 395–396. |
| Token flow: backend mints token, frontend connects | VERIFIED | README lines 487–511; warning lines 513–514; public token endpoint docs `/v1/single-use-token/{token_type}`. |
| Treat `AUTH_ERROR`, `QUOTA_EXCEEDED`, `CLOSE` as first-class | VERIFIED | Events exist in connection.ts lines 108, 116, 114. |
| “Re-auth then reconnect with backoff; preserve pending segments” | OPINIONATED-BEST-PRACTICE | Sound operational guidance; not a mandated SDK behavior. |

### `04_MULTILINGUAL_SUBTITLE_ARCHITECTURE.md`

| Claim | Class | Evidence |
|---|---|---|
| Scribe emits partial + committed events suitable for two-lane subtitle architecture | VERIFIED | connection.ts events lines 102–106; README lines 596–611. |
| `partial_draft`/`commit_final` event contract | OPINIONATED-BEST-PRACTICE | Custom schema, not ElevenLabs-native schema. |
| Translate partials at capped rate and finals on commit | OPINIONATED-BEST-PRACTICE | Implementation strategy; no direct vendor contract. |
| Preserve entities/glossary for domain stabilization | OPINIONATED-BEST-PRACTICE | QA/translation recommendation, not SDK-specific. |
| Latency metrics list is required for optimization | OPINIONATED-BEST-PRACTICE | Operational recommendation. |

### `05_OPERATIONS_CHECKLIST.md`

| Claim | Class | Evidence |
|---|---|---|
| Keep API key backend-only; mint tokens server-side | VERIFIED | client README auth examples and warnings (lines 74–89, 119–127, 487–514). |
| Handle `onError` / `ERROR` / `AUTH_ERROR` / `CLOSE` | VERIFIED | Conversation callbacks + Scribe events exist. |
| `languageCode: "en"` when source known | PARTIALLY VERIFIED | Option exists (`scribe.ts` lines 55–58, 166–168); performance effect is best-practice inference. |
| `includeTimestamps` when timing matters | VERIFIED | option and event behavior documented in README lines 607–611, 660. |
| `previousText` first chunk only | VERIFIED | README lines 751–756; connection.ts lines 395–396. |
| `@elevenlabs/agents-cli` in monorepo is “marked deprecated” | OUTDATED | Package absent from local monorepo; not present as deprecated package. |

### `06_REFERENCE_CONFIGS_AND_PIPELINE.md`

| Claim | Class | Evidence |
|---|---|---|
| `SCRIBE_MODEL_ID=scribe_v2_realtime` baseline | VERIFIED | client README Scribe examples use `scribe_v2_realtime` (lines 525, 546, 648, 705, 725). |
| Backend endpoints should mint conversation/scribe credentials | PARTIALLY VERIFIED | Strongly aligned with vendor examples; exact `/api/...` paths are local convention. |
| Scribe token response may include token/expires/model_id shape | PARTIALLY VERIFIED | Token field verified; `expires_at`/`model_id` are app-envelope additions (not guaranteed by vendor response in README sample). |
| Reconnect policy on auth/token expiry with replay strategy | OPINIONATED-BEST-PRACTICE | Operational pattern; SDK offers primitives only. |
| Append-only final timeline storage | OPINIONATED-BEST-PRACTICE | Architecture guidance, not vendor contract. |

### `07_EVALUATION_AND_ACCEPTANCE.md`

| Claim | Class | Evidence |
|---|---|---|
| Metric families (latency/quality/reliability) are relevant | OPINIONATED-BEST-PRACTICE | Strong QA guidance, but not SDK claims. |
| Suggested thresholds (e.g., WER<=18%, p95 bounds) | OPINIONATED-BEST-PRACTICE | No vendor guarantee in upstream docs. |
| AUTH/TRANSPORT/ASR/etc taxonomy | OPINIONATED-BEST-PRACTICE | Incident-management guidance. |

### `08_API_ENDPOINTS_AND_ERROR_SURFACE_APPENDIX.md`

| Claim | Class | Evidence |
|---|---|---|
| Signed URL endpoint: `GET /v1/convai/conversation/get-signed-url?agent_id=...` + `xi-api-key` | VERIFIED | client README lines 81–89; public docs/search references to get-signed-url endpoint. |
| Conversation token endpoint: `GET /v1/convai/conversation/token?agent_id=...` + `xi-api-key` | VERIFIED | client README lines 119–127; web search result for official API ref includes same path and GET method. |
| Scribe token endpoint: `POST /v1/single-use-token/realtime_scribe` | VERIFIED | client README lines 491–499; public tokens API docs `/v1/single-use-token/{token_type}` with enum `realtime_scribe`. |
| App-facing endpoint examples (`/signed-url`, `/conversation-token`, `/scribe-token`) | OPINIONATED-BEST-PRACTICE | Boundary design recommendation, not vendor requirement. |
| Runtime events mapped: `AUTH_ERROR`, `ERROR`, `QUOTA_EXCEEDED`, `CLOSE` | VERIFIED | connection.ts enum lines 108–116, 114. |

### `09_VERSION_PINNING_AND_COMPATIBILITY_MATRIX.md`

| Claim | Class | Evidence |
|---|---|---|
| Exact version pinning + lockfile policy | OPINIONATED-BEST-PRACTICE | Standard release governance guidance. |
| Compatibility checks should include auth flow/event shape/token flow | PARTIALLY VERIFIED | Good practice; token/auth/event names are verified upstream, but “must” policy is local process choice. |
| Current tracked upstream commit hash | VERIFIED | matches local git HEAD. |
| `packages/agents-cli` absent in this revision | VERIFIED | local package directory listing. |

---

## Local repo vs public docs/web divergence observed

1. **Agents CLI presence differs by source**
   - **Local monorepo snapshot:** no `packages/agents-cli` directory.
   - **Public web signals:** Brave results show references to `@elevenlabs/agents-cli` (GitHub snippet and changelog entries), indicating CLI exists in broader public ecosystem even if absent from this specific repo snapshot.
   - **Practical interpretation:** do not infer global deprecation/removal of the npm package solely from this monorepo revision.

2. **Docs URL/path churn in ElevenLabs docs site**
   - Some links returned 404 via fetch while search still indexes related pages (e.g., agents-platform auth/websocket docs URLs).
   - API reference pages for STT realtime and token creation are accessible and consistent with core claims.
   - **Practical interpretation:** endpoint claims here are still supported, but docs URL stability is imperfect; maintain periodic link validation.

---

## Ambiguous areas requiring caution

1. **`languageCode` benefit claims**
   - Option exists and is documented; expected quality/latency gains are context-dependent and not guaranteed.

2. **Scribe token response fields beyond `token`**
   - Vendor examples consistently show `token`; fields like `expires_at` and app-level `model_id` are useful but not guaranteed by raw upstream API contract.

3. **Operational SLO thresholds in 07**
   - Reasonable defaults, but should be treated as team-specific gates, not product guarantees.

4. **Widget package verification via README paths**
   - Widget packages exist; README path references in docs are stale in this local checkout.

---

## Final recommendation (overall)

**Overall judgment:** The docs pack is **largely reliable and implementation-safe** for conversation + Scribe integrations.  
Most factual claims about endpoint paths, methods, auth model, options, and event names are well aligned with upstream code/docs.

**Must-fix before treating as “fully clean”:**
1. Update `05_OPERATIONS_CHECKLIST.md` wording about `@elevenlabs/agents-cli` (use “absent in this monorepo revision,” not “deprecated in monorepo”).
2. Fix stale widget canonical source pointers in `00_INDEX.md` (or replace with package docs URLs / source entry files).

After those corrections, this pack is a strong implementation reference.
