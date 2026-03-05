# Trilinos Build/Install Doc Navigation Map

## Scope
Single-page router that maps common build/install intents to the exact next document to open in this Trilinos docs pack.

## Intent → primary doc → secondary docs

- **I need the fastest first successful build/install path**
  - Primary: `32_30_MINUTE_FIRST_SUCCESS_PATH.md`
  - Secondary: `04_BUILD_INSTALL_PLAYBOOK.md`, `29_INSTALL_VERIFICATION_CHECKLIST.md`

- **I need executable command snippets right now**
  - Primary: `33_BUILD_AND_INSTALL_COMMAND_CHEATSHEET.md`
  - Secondary: `13_CMAKE_FLAG_QUICK_REFERENCE.md`, `30_CMAKE_CONFIGURE_SCRIPT_TEMPLATE_LIBRARY.md`

- **I need to choose MPI vs non-MPI and package scope**
  - Primary: `26_BUILD_INSTALL_DECISION_TREE.md`
  - Secondary: `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`, `17_PACKAGE_SELECTION_STRATEGY.md`, `40_BUILD_PROFILE_SELECTION_MATRIX.md`

- **Configure fails on missing deps/TPLs**
  - Primary: `31_TPL_DISCOVERY_AND_PATH_HINTS.md`
  - Secondary: `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`, `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`, `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`

- **I suspect stale configure state after changing compiler/MPI/TPL flags**
  - Primary: `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`
  - Secondary: `35_PRECONFIGURE_ENVIRONMENT_CHECKLIST.md`, `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`

- **I’m seeing MPI wrapper/link/runtime inconsistencies**
  - Primary: `44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md`
  - Secondary: `39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`, `27_DOWNSTREAM_INTEGRATION_FAILURE_PATTERNS.md`

- **I need compiler/C++ standard mismatch triage**
  - Primary: `46_COMPILER_AND_CXX_STANDARD_ALIGNMENT_GUIDE.md`
  - Secondary: `13_CMAKE_FLAG_QUICK_REFERENCE.md`, `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`

- **Build/install works, but downstream app cannot find/link Trilinos**
  - Primary: `27_DOWNSTREAM_INTEGRATION_FAILURE_PATTERNS.md`
  - Secondary: `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`, `29_INSTALL_VERIFICATION_CHECKLIST.md`, `39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`

- **I need a strict triage sequence under time pressure**
  - Primary: `48_TRIAGE_DECISION_ORDER_BUILD_INSTALL_ISSUES.md`
  - Secondary: `26_BUILD_INSTALL_DECISION_TREE.md`, `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`

- **I need a complete log bundle + minimal reproducible case**
  - Primary: `43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md`
  - Secondary: `37_LLM_BUILD_INSTALL_PROMPT_TEMPLATES.md`, `45_BUILD_INSTALL_ESCALATION_HANDOFF_CHECKLIST.md`

- **I need escalation-ready handoff content**
  - Primary: `45_BUILD_INSTALL_ESCALATION_HANDOFF_CHECKLIST.md`
  - Secondary: `43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md`, `39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`

- **I need to reduce CI/local cycle time**
  - Primary: `28_CI_AND_LOCAL_BUILD_TIME_REDUCTION_GUIDE.md`
  - Secondary: `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`, `21_CANONICAL_WORKFLOWS_BY_USER_GOAL.md`

## Recommended reading sequence (new user)
1. `10_GETTING_STARTED_ROUTE.md`
2. `26_BUILD_INSTALL_DECISION_TREE.md`
3. `32_30_MINUTE_FIRST_SUCCESS_PATH.md`
4. `33_BUILD_AND_INSTALL_COMMAND_CHEATSHEET.md`
5. `29_INSTALL_VERIFICATION_CHECKLIST.md`
6. `34_BUILD_INSTALL_DOC_NAVIGATION_MAP.md`

## Provenance
- Navigation synthesized from existing `library/Trilinos/` build/install and troubleshooting docs in this workspace.
