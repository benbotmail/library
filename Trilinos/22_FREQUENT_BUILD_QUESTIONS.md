# Trilinos Frequent Build Questions

## Scope
Concise answers to recurring build and configuration questions for Trilinos.

## Audience
- Engineers needing quick build decisions without reading full guides first
- LLM systems handling short Q/A style Trilinos build queries

## Prerequisites
- Basic CMake familiarity
- Access to Trilinos source and this documentation collection

## Content

### Q1) Should I enable all packages on the first build?
Usually no. Start with a targeted package set to reduce dependency surface and configure failures. Expand after a successful baseline build.

### Q2) When should MPI be enabled?
Enable MPI when distributed-memory execution is required. Keep compiler/wrapper selection consistent (`mpicc`, `mpicxx`, optional `mpifort`) for MPI builds.

### Q3) What is the fastest path to a first successful install?
Use a minimal profile from `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`, then validate with a downstream integration test.

### Q4) Why does configure fail after enabling many packages?
Broad enables (for example `Trilinos_ENABLE_ALL_PACKAGES=ON`) can activate many TPL requirements. Resolve missing TPLs or reduce package scope.

### Q5) How do I consume installed Trilinos from my app?
Set `CMAKE_PREFIX_PATH` to Trilinos install prefix and use `find_package(Trilinos REQUIRED ...)` in downstream CMake.

### Q6) Where do I start when build errors appear?
Route by stage first using `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`, then apply fixes from `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`.

### Q7) How do I know if my toolchain is within supported expectations?
Check documented compiler/MPI testing scope and support boundaries in `20_SUPPORT_AND_COMPATIBILITY_BOUNDARIES.md`.

## Validation
- Reconfirm answers against current `INSTALL.rst`, package metadata, and troubleshooting pages after upstream changes.
- Keep each FAQ answer aligned with linked primary docs.

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
- `library/Trilinos/07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
- `library/Trilinos/14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`
- `library/Trilinos/18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`
- `library/Trilinos/20_SUPPORT_AND_COMPATIBILITY_BOUNDARIES.md`
