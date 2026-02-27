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
2. `04_BUILD_INSTALL_PLAYBOOK.md`
3. `13_CMAKE_FLAG_QUICK_REFERENCE.md`
4. `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`

### Goal: Choose a safe initial package set
1. `17_PACKAGE_SELECTION_STRATEGY.md`
2. `05_PACKAGE_ROUTING_AND_TIERS.md`
3. `03_PACKAGE_CATALOG.md`

### Goal: Resolve configure/build failures quickly
1. `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`
2. `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
3. `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
4. `13_CMAKE_FLAG_QUICK_REFERENCE.md`

### Goal: Build downstream app against installed Trilinos
1. `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
2. `04_BUILD_INSTALL_PLAYBOOK.md`
3. `13_CMAKE_FLAG_QUICK_REFERENCE.md`

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
