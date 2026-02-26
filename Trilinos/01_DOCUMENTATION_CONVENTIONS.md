# Trilinos Documentation Conventions (LLM-Friendly)

## Purpose
This document defines the documentation standard used by the Trilinos docs pack so pages stay consistent, trustworthy, and easy to retrieve.

## Page structure standard
Each substantive page should include:
1. **Scope** — what the page covers and does not cover.
2. **Audience** — who should use the page.
3. **Prerequisites** — tools/context required.
4. **Main content** — procedures, explanations, and examples.
5. **Provenance** — source files/URLs used to support claims.

## Claim confidence labeling
When useful, claims should be implicitly grounded by source type:
- **Official-doc**: trilinos.github.io or official published references.
- **Repo-doc**: upstream repository docs (`README`, `INSTALL`, `doc/`, etc.).
- **Code-inferred**: derived from build/package metadata in source files.
- **Community/wiki**: useful but lower-authority references.

## Command snippet rules
- Prefer minimal, reproducible snippets.
- State assumptions (MPI vs non-MPI, compiler expectations).
- Avoid environment-specific hardcoding.
- Keep examples copy/paste ready.

## Troubleshooting content rules
- Start with observable error pattern.
- Provide likely causes ordered by frequency.
- Give shortest safe fix path first.
- Include escalation pointers when uncertainty remains.

## Provenance requirement
Non-trivial guidance should be traceable to at least one listed source path or URL.

## Scope boundary
This pack is documentation-focused. It does not define project management workflow, scheduling policy, or internal task orchestration.
