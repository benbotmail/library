# Trilinos Retrieval Entrypoints (Build/Install)

## Scope
High-signal entrypoints for LLM retrieval when the user asks build/install questions with limited context.

## Purpose
When a query is short or ambiguous, this page selects the best first document to retrieve so answers stay fast and source-aligned.

## Query cue → first retrieval target

- **"How do I build Trilinos quickly?"**
  - `33_BUILD_AND_INSTALL_COMMAND_CHEATSHEET.md`

- **"I just need first success" / "new machine setup"**
  - `32_30_MINUTE_FIRST_SUCCESS_PATH.md`

- **"Which profile should I start with?"**
  - `40_BUILD_PROFILE_SELECTION_MATRIX.md`

- **"MPI or non-MPI?" / "which packages first?"**
  - `26_BUILD_INSTALL_DECISION_TREE.md`

- **"Configure failed"**
  - `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`

- **"Missing TPL / dependency not found"**
  - `31_TPL_DISCOVERY_AND_PATH_HINTS.md`

- **"Install succeeded but app can’t find Trilinos"**
  - `27_DOWNSTREAM_INTEGRATION_FAILURE_PATTERNS.md`

- **"Runtime library error / .so not found"**
  - `39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`

- **"Need CI faster" / "builds take too long"**
  - `28_CI_AND_LOCAL_BUILD_TIME_REDUCTION_GUIDE.md`

## Secondary fallback order (if first doc is insufficient)
1. `34_BUILD_INSTALL_DOC_NAVIGATION_MAP.md`
2. `19_CROSS_REFERENCE_MATRIX.md`
3. `23_REFERENCE_LINKS_AND_ANCHORS.md`

## Provenance
- Routing synthesized from existing build/install, troubleshooting, and navigation pages in `library/Trilinos/`.
