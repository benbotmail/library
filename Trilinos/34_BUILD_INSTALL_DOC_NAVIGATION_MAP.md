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
  - Secondary: `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`, `17_PACKAGE_SELECTION_STRATEGY.md`

- **Configure fails on missing deps/TPLs**
  - Primary: `31_TPL_DISCOVERY_AND_PATH_HINTS.md`
  - Secondary: `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`, `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`

- **Build/install works, but downstream app cannot find/link Trilinos**
  - Primary: `27_DOWNSTREAM_INTEGRATION_FAILURE_PATTERNS.md`
  - Secondary: `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`, `29_INSTALL_VERIFICATION_CHECKLIST.md`

- **I need to reduce CI/local cycle time**
  - Primary: `28_CI_AND_LOCAL_BUILD_TIME_REDUCTION_GUIDE.md`
  - Secondary: `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`, `21_CANONICAL_WORKFLOWS_BY_USER_GOAL.md`

## Recommended reading sequence (new user)
1. `10_GETTING_STARTED_ROUTE.md`
2. `26_BUILD_INSTALL_DECISION_TREE.md`
3. `32_30_MINUTE_FIRST_SUCCESS_PATH.md`
4. `33_BUILD_AND_INSTALL_COMMAND_CHEATSHEET.md`
5. `29_INSTALL_VERIFICATION_CHECKLIST.md`

## Provenance
- Navigation synthesized from existing `library/Trilinos/` build/install and troubleshooting docs in this workspace.
