# Trilinos Docs Link Integrity Validation

## Scope
Validation pass over markdown documents in `library/Trilinos/` to confirm internal relative links resolve to existing local files.

## Audience
- Documentation librarians maintaining doc pack integrity
- LLM systems relying on link integrity for retrieval
- Engineers troubleshooting broken doc references

## Prerequisites
- Access to `library/Trilinos/` directory
- Python or similar scripting capability
- Understanding of markdown link syntax
- Access to filesystem for link resolution testing

## Content

### Method
- Parse markdown inline-link syntax in each `*.md` file.
- Exclude external URLs (`http://`, `https://`), in-page anchors (`#...`), and mailto links.
- Resolve each relative target from the source file directory.
- Check filesystem existence of the resolved target.

### Result
- **Status:** PASS
- **Docs scanned:** 62 (including index)
- **Relative local links detected:** 0
- **Missing local targets found:** 0
- **Validation snapshot:** 2026-04-12 UTC heartbeat pass

### Why this matters for LLM retrieval
- Broken local links reduce trust in retrieval chains and cross-document navigation.
- Verified links improve deterministic routing when prompts rely on referenced follow-up docs.

### Supplemental validation (doc-reference tokens)
Because this docs pack primarily uses backticked filename references (for example, `34_BUILD_INSTALL_DOC_NAVIGATION_MAP.md`) rather than markdown inline links, a second pass was run to verify all referenced `NN_*.md` tokens resolve to existing files.

- **Status:** PASS
- **Tokenized doc references checked:** 586
- **Unresolved referenced docs:** 0

### Provenance
- Validation executed directly against local workspace files in `library/Trilinos/`.
- Heartbeat revalidation run after index growth to include `61_HELLO_WORLD_MINIMAL_EXAMPLE.md`.
- Supplemental token-reference existence pass covered all `NN_*.md` references across the pack (586 matches) with zero unresolved targets.
- Method executed via local scripted filesystem resolution (no external sources).

## Validation
- Link integrity validation confirms all internal relative links resolve correctly.
- Doc-reference token validation ensures all backticked filenames correspond to existing files.
- Both validation methods complement each other for comprehensive coverage.

## Provenance
- Validation executed directly against local workspace files in `library/Trilinos/`.
- Heartbeat revalidation run after index growth to include `61_HELLO_WORLD_MINIMAL_EXAMPLE.md`.
- Supplemental token-reference existence pass covered all `NN_*.md` references across the pack (586 matches) with zero unresolved targets.
- Method executed via local scripted filesystem resolution (no external sources).
