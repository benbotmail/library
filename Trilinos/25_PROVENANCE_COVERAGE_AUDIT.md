# Trilinos Docs Provenance Coverage Audit

## Scope
Audit of `library/Trilinos/*.md` to verify documentation pages include a provenance section for source grounding.

## Audit rule
A page passes if it contains one of:
- `## Provenance`
- `## Source provenance`
- `## Sources`
- `## Provenance notes`

`00_INDEX.md` is treated as a navigational index and excluded from the requirement.

## Results
- Pages checked (excluding index): **52**
- Pages missing provenance section: **0**
- Overall status: **PASS**

## Why this matters for LLM-doc usage
- Provenance sections improve traceability for factual claims.
- Retrieval chains remain auditable when each page advertises where assertions came from.

## Provenance
- Audit executed against local markdown files in `library/Trilinos/` in this workspace.
- Validation pass updated after adding `52_RUNTIME_LOADER_RPATH_TRIAGE.md`.
