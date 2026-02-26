# Trilinos Documentation Conventions

## Scope
Documentation standards for pages in this Trilinos LLM-friendly collection.

## Audience
- Engineers authoring or refining Trilinos documentation pages
- LLM systems consuming this collection for reliable retrieval

## Prerequisites
- Familiarity with Trilinos source and documentation locations
- Ability to cite repo paths and official URLs in provenance sections

## Content

### Required page structure
Each substantive page should use this heading order:
1. `## Scope`
2. `## Audience`
3. `## Prerequisites`
4. `## Content`
5. `## Validation` (optional but preferred)
6. `## Provenance`

### Claim grounding model
Ground non-trivial claims using one of these source types:
- **official-doc**: `trilinos.github.io` and official published references
- **repo-doc**: upstream repository docs (`README`, `INSTALL`, `doc/`, etc.)
- **code-inferred**: build/package metadata inferred from source files
- **community/wiki**: lower-authority contextual references

When source confidence differs, prioritize higher-authority sources in narrative and provenance.

### Command snippet rules
- Keep snippets minimal and reproducible.
- State assumptions (MPI vs non-MPI, compiler/toolchain expectations).
- Avoid environment-specific hardcoding where avoidable.
- Keep command blocks copy/paste safe.

### Troubleshooting writing rules
- Start with observable error pattern.
- List likely causes in practical order.
- Provide shortest safe fix path first.
- Include escalation guidance when uncertainty remains.

### Documentation boundary
This collection is documentation-only. Exclude planning notes, progress logs, and workflow orchestration notes.

## Validation
- Confirm required section headings exist before considering a page complete.
- Confirm non-trivial claims are backed by listed provenance.
- Confirm file naming and tone remain documentation-oriented.

## Provenance
- `library/Trilinos/00_INDEX.md`
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/PackagesList.cmake`
- Official docs index: <https://trilinos.github.io/documentation.html>
