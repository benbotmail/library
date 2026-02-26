# 07 — Gaps Checklist and Research Notes

This file captures what was missing for LLM-friendly implementation planning and what to verify during integration.

## What this pack already covers well
- Correct core claim: no single realtime STT endpoint that directly outputs translated text
- Practical architecture choices (Realtime STT + external translation)
- Batch dubbing path for non-interactive localization
- Basic latency/cost/reliability checklist

## Key gaps that needed explicit documentation

### 1) Internal event schema + idempotency
Missing before: a canonical event contract for downstream services.
Why it matters: avoids coupling to vendor event shapes and simplifies retries/reordering.

### 2) Multi-target translation fan-out behavior
Missing before: guidance for parallel targets and non-blocking delivery.
Why it matters: production systems often need 2+ target languages.

### 3) Failure semantics per stage
Missing before: explicit fallback behavior for STT disconnects vs translation timeouts.
Why it matters: keeps UX predictable under partial outages.

### 4) Latency budget decomposition
Missing before: measurable bucket-level latency targets.
Why it matters: helps teams optimize the actual bottleneck, not guess.

### 5) Pre-production acceptance criteria
Missing before: concrete go-live checks.
Why it matters: prevents shipping “works on demo” pipelines.

## Verification notes from current docs research

Confirmed in ElevenLabs docs:
- Realtime STT is WebSocket-based at `/v1/speech-to-text/realtime`
- Commit strategies are `manual` and `vad`
- `previous_text` context can be sent with first audio chunk only
- Realtime supports audio formats including `pcm_16000` and `ulaw_8000`
- Realtime events include partial and committed transcript variants
- Auth modes: `xi-api-key` header or single-use token query param

## Open questions to resolve during implementation

1. **Translator choice per language pair**
   - Which provider/model wins on latency-quality-cost for your dominant language routes?

2. **Segmentation policy tuning**
   - For your audio characteristics, does VAD produce natural translation chunks or over-segmentation?

3. **Code-switch behavior**
   - How often do users switch languages mid-utterance, and how should UI display this?

4. **Glossary governance**
   - Who owns term lock lists and how are changes versioned?

5. **Retention/compliance requirements**
   - Should raw audio/transcripts be persisted, redacted, or not stored at all?

## Suggested next docs to add (if you want full implementation kit)

- `08_EVALUATION_HARNESS.md` — test corpus + scoring rubric (WER, latency, translation adequacy)
- `09_RUNBOOK_INCIDENTS.md` — operator runbook for outage/debug scenarios
- `10_CLIENT_INTEGRATION_RECIPES.md` — browser/mobile/telephony adapter patterns
