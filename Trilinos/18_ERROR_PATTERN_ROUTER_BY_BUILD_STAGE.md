# Trilinos Error Pattern Router by Build Stage

## Scope
Stage-based routing guide for quickly locating the right troubleshooting path from observed failure timing.

## Audience
- Engineers triaging Trilinos build issues under time pressure
- LLM systems mapping error context to the most relevant troubleshooting document

## Prerequisites
- Access to configure/build/test logs
- Basic understanding of Trilinos build stages (configure, compile/link, test, downstream integration)

## Content

### Stage 1: Configure-time failures
Typical indicators:
- Missing TPL messages
- Invalid package enable names
- MPI/compiler detection failures

Route to:
- `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
- `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
- `13_CMAKE_FLAG_QUICK_REFERENCE.md`

### Stage 2: Compile/link-time failures
Typical indicators:
- Unresolved symbols or linker errors
- ABI/toolchain mismatch behavior
- Build instability with broad package sets

Route to:
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`
- `17_PACKAGE_SELECTION_STRATEGY.md`

### Stage 3: Test-time failures
Typical indicators:
- MPI runtime launch issues
- Tests failing after successful build

Route to:
- `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
- `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`

### Stage 4: Downstream integration failures
Typical indicators:
- `find_package(Trilinos)` failure
- Consumer app compile failures against installed Trilinos

Route to:
- `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
- `13_CMAKE_FLAG_QUICK_REFERENCE.md`

### Stage 5: Ownership or contribution process blockers
Typical indicators:
- Unclear package owner
- Questions about PR target branches or security disclosure path

Route to:
- `03_PACKAGE_CATALOG.md`
- `08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md`

## Validation
- Verify stage mapping remains aligned with troubleshooting and build docs whenever those docs are updated.
- Verify linked target files exist and are indexed in `00_INDEX.md`.

## Provenance
- `library/Trilinos/00_INDEX.md`
- `library/Trilinos/03_PACKAGE_CATALOG.md`
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
- `library/Trilinos/07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
- `library/Trilinos/08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md`
- `library/Trilinos/09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
- `library/Trilinos/13_CMAKE_FLAG_QUICK_REFERENCE.md`
- `library/Trilinos/14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`
- `library/Trilinos/17_PACKAGE_SELECTION_STRATEGY.md`
