# 07 — Evaluation and Acceptance (LLM-Ready QA Playbook)

Use this to grade transcript + translation quality before declaring the stack reliable.

## 1) Evaluation goals

Primary:
- Accurate committed transcript
- Stable low latency
- High domain-term recall
- Deterministic subtitle replacement semantics

Secondary:
- Readability and punctuation quality
- Graceful degradation under reconnect/timeout scenarios

---

## 2) Test set design

Build at least 3 buckets of audio:
1. **Clean speech** (single speaker, good mic)
2. **Realistic conference** (cross-talk, room noise)
3. **Domain-heavy** (proper nouns, scientific terms, mixed accents)

Minimum target duration:
- 60–90 minutes total audio
- at least 20 minutes in each bucket

---

## 3) Metrics to record

### Latency
- `audio_to_partial_ms` (p50/p95)
- `audio_to_commit_ms` (p50/p95)
- `commit_to_translation_ms` (p50/p95 by language)
- `end_to_end_visible_ms` (p50/p95 by language)

### Quality
- Word Error Rate (WER) on committed source transcript
- Domain term recall rate
- Translation adequacy score (human 1–5)
- Partial churn rate (how often drafts change before final)

### Reliability
- reconnect success rate
- token-expiry recovery success
- dropped/final-missing segment rate

---

## 4) Suggested acceptance thresholds (initial)

Tune as needed, but good starting gates:

- Source WER: <= 18% on realistic conference audio
- Domain term recall: >= 90% for glossary items
- p95 `audio_to_commit_ms`: <= 2200 ms
- p95 `commit_to_translation_ms`: <= 1800 ms
- Final-missing segment rate: < 0.5%
- Reconnect recovery success: >= 99%

If one language consistently misses thresholds, isolate and tune that lane without blocking others.

---

## 5) Error taxonomy for triage

Classify every notable failure as one of:
- **AUTH** (token issuance/expiry/invalid)
- **TRANSPORT** (disconnect/timeouts)
- **ASR_QUALITY** (recognition errors)
- **TRANSLATION_QUALITY** (semantic drift/omissions)
- **PIPELINE_ORDERING** (out-of-order or duplicate finals)
- **UI_SYNC** (display not matching event contract)

This prevents vague incident reports and speeds remediation.

---

## 6) Human review rubric (fast)

For each sampled segment, reviewer answers:
1. Is final source transcript faithful? (Y/N)
2. Are specialist terms correct? (Y/N)
3. Is translation meaning preserved? (1–5)
4. Is subtitle timing acceptable for live viewing? (1–5)
5. Any harmful hallucination or missing critical phrase? (Y/N + note)

Store reviews as structured JSON for trend analysis.

---

## 7) Go/No-Go checklist

Go when all are true:
- thresholds met on two consecutive evaluation runs
- no open P1/P2 issues in AUTH/TRANSPORT categories
- glossary and domain-term handling validated on latest dataset
- ops dashboards show stable p95 trends for at least 24 hours

No-Go when any are true:
- recurring token/auth breakage
- sustained p95 latency regression >20%
- final transcript missing-segment issues above threshold
- domain-term recall drops below agreed baseline

---

## 8) Post-deploy guardrails

After launch:
- run daily automated metric report
- run weekly human spot review of random committed segments
- maintain changelog of config changes tied to metric shifts
- require rollback path for any model/pipeline config update

This keeps quality measurable and prevents silent drift.