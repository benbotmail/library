# Trilinos Canonical Workflows by User Goal

## Scope
Canonical workflow paths mapped to common user goals, so users and LLM systems can pick the shortest correct documentation path.

## Audience
- Engineers with a specific objective (install, integrate, troubleshoot, contribute)
- LLM systems selecting high-value document sequences

## Prerequisites
- Access to this docs collection (`library/Trilinos/`)
- Basic understanding of whether the target is build-time, runtime, or contribution workflow

## Content

### Goal: Get a first successful Trilinos install
1. `10_GETTING_STARTED_ROUTE.md`
2. `56_PLATFORM_BASELINE_AND_TOOLCHAIN_MINIMUMS.md`
3. `04_BUILD_INSTALL_PLAYBOOK.md`
4. `55_KNOWN_GOOD_STARTER_CONFIGS.md`
5. `13_CMAKE_FLAG_QUICK_REFERENCE.md`

### Goal: Choose a safe initial package set
1. `17_PACKAGE_SELECTION_STRATEGY.md`
2. `05_PACKAGE_ROUTING_AND_TIERS.md`
3. `03_PACKAGE_CATALOG.md`

### Goal: Resolve configure/build failures quickly
1. `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md`
2. `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`
3. `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
4. `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
5. `13_CMAKE_FLAG_QUICK_REFERENCE.md`

### Goal: Build downstream app against installed Trilinos
1. `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
2. `04_BUILD_INSTALL_PLAYBOOK.md`
3. `13_CMAKE_FLAG_QUICK_REFERENCE.md`

### Goal: Make local and CI configure workflows reproducible
1. `58_CMAKE_PRESETS_ADOPTION_GUIDE.md`
2. `56_PLATFORM_BASELINE_AND_TOOLCHAIN_MINIMUMS.md`
3. `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`
4. `55_KNOWN_GOOD_STARTER_CONFIGS.md`

### Goal: Triage preset-specific failures (local/CI divergence)
1. `59_CMAKE_PRESETS_FAILURE_PATTERNS.md`
2. `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md`
3. `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`
4. `44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md`

### Goal: Understand support boundaries and risk posture
1. `20_SUPPORT_AND_COMPATIBILITY_BOUNDARIES.md`
2. `02_SOURCE_OF_TRUTH_MAP.md`
3. `08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md`

### Goal: Contribute or report security issues
1. `08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md`
2. `03_PACKAGE_CATALOG.md`
3. `02_SOURCE_OF_TRUTH_MAP.md`

## Validation
- Re-check workflow sequences whenever referenced docs are renamed or structurally changed.
- Confirm each referenced file exists and is indexed in `00_INDEX.md`.

## Provenance
- `library/Trilinos/00_INDEX.md`
- `library/Trilinos/10_GETTING_STARTED_ROUTE.md`
- `library/Trilinos/18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`
- `library/Trilinos/19_CROSS_REFERENCE_MATRIX.md`
