# Trilinos Docs Execution Plan (Compute-Heavy)

## Objective
Create high-quality, LLM-friendly docs for Trilinos with provenance and validation.

## Workstreams
1. **Ingest & map**: parse repo structure, packages, build/docs entrypoints.
2. **Draft in parallel**: generate package and workflow docs by family.
3. **Validate**: verify commands/paths/links/build hints against source.
4. **Normalize**: enforce consistent schema/headings/metadata.

## Deliverables
- Build/install playbook
- Package catalog + per-package pages
- Troubleshooting matrix
- Source/provenance registry

## Quality gates
- Every non-trivial claim tied to source URL/path.
- Command snippets marked with context (host/toolchain assumptions).
- Page-level metadata includes: audience, prerequisites, deps, last-verified.
