# Trilinos Documentation Quality Checklist

## Scope
Checklist for validating consistency, provenance, and retrieval quality across this Trilinos documentation collection.

## Audience
- Engineers maintaining this docs pack
- LLM systems or tooling validating document quality gates

## Prerequisites
- Access to all files in `library/Trilinos/`
- Ability to verify claims against upstream Trilinos sources

## Content

### Structural checklist
- Each substantive file includes:
  - `## Scope`
  - `## Audience`
  - `## Prerequisites`
  - `## Content`
  - `## Validation`
  - `## Provenance`
- `00_INDEX.md` lists every file with a short description.

### Provenance checklist
- Non-trivial claims are backed by at least one source path or URL.
- Source priority follows `02_SOURCE_OF_TRUTH_MAP.md`.
- External references are still reachable and relevant.

### Retrieval checklist
- Headings are stable and descriptive.
- Sections are atomic and task-focused.
- Command snippets are copy/paste-safe and include assumptions.
- Troubleshooting sections use symptom → cause → fix flow.

### Content hygiene checklist
- No planning/worklog/progress language in docs pages.
- Filenames remain documentation-oriented and topic-specific.
- Terminology is consistent with `12_TERMS_AND_METADATA_GLOSSARY.md`.

## Validation
- Run periodic schema checks over all files.
- Re-verify package/TPL metadata after upstream Trilinos updates.
- Update index and routing docs whenever files are added or renamed.

## Provenance
- `library/Trilinos/00_INDEX.md`
- `library/Trilinos/01_DOCUMENTATION_CONVENTIONS.md`
- `library/Trilinos/02_SOURCE_OF_TRUTH_MAP.md`
- `library/Trilinos/11_LLM_QUERY_ROUTING_HINTS.md`
- `Trilinos/README.md`
- Official docs index: <https://trilinos.github.io/documentation.html>
