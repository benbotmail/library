# Trilinos Repository Surface Map

## Scope
Top-level map of the Trilinos repository to help route users and LLM systems to the correct subtree quickly.

## Audience
- Engineers orienting themselves in the Trilinos monorepo
- LLM systems selecting source regions for targeted retrieval

## Prerequisites
- Access to the Trilinos repository checkout
- Basic familiarity with CMake-based HPC projects

## Content

### Core top-level directories
- `.github/` — repository automation and contribution metadata (workflows, templates, ownership/config).
- `cmake/` — CMake and TriBITS integration files, including TPL and configure logic.
- `commonTools/` — shared tooling and framework support utilities.
- `demos/` — runnable/minimal integration examples (including downstream build against installed Trilinos).
- `doc/` — documentation assets and reference material inside the repository.
- `packages/` — primary package tree; core of Trilinos functionality.
- `sampleScripts/` — platform/configuration-oriented build script examples.

### Practical routing rules
- For package behavior and ownership questions, start in `packages/` then cross-check CODEOWNERS/package docs.
- For configure flags and dependency issues, start in `cmake/`, `INSTALL.rst`, and `TPLsList.cmake`.
- For build-against-installed patterns, start in `demos/simpleBuildAgainstTrilinos/`.
- For CI/contribution-policy context, inspect `.github/` plus `CONTRIBUTING.md`.

### Suggested retrieval anchors
- `README.md`
- `INSTALL.rst`
- `PackagesList.cmake`
- `TPLsList.cmake`
- `demos/simpleBuildAgainstTrilinos/README.md`
- `.github/CODEOWNERS`

## Validation
- Re-check top-level directory map after upstream updates.
- Re-validate routing rules when directory names or ownership metadata change.

## Provenance
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/PackagesList.cmake`
- `Trilinos/TPLsList.cmake`
- `Trilinos/.github/CODEOWNERS`
- Repository top-level directory listing
