# Trilinos CMake Flag Quick Reference

## Scope
Quick-reference list of high-utility CMake flags used in common Trilinos configure workflows.

## Audience
- Engineers configuring Trilinos builds repeatedly
- LLM systems answering flag-level configure questions

## Prerequisites
- Basic CMake usage
- Access to Trilinos source tree and intended compiler/toolchain

## Content

### Baseline configure flags
- `-DCMAKE_INSTALL_PREFIX=<path>`
  - Install destination for Trilinos artifacts and CMake package config files.
- `-DCMAKE_C_COMPILER=<path>`
- `-DCMAKE_CXX_COMPILER=<path>`
- `-DCMAKE_Fortran_COMPILER=<path>` (optional)

### MPI-related flags
- `-DTPL_ENABLE_MPI=ON`
  - Enables MPI support in Trilinos configure path.
- `-DMPI_BASE_DIR=<path>`
  - Points configure to MPI installation root (alternative to compiler wrapper-only setup).

### Package enable flags
- `-DTrilinos_ENABLE_ALL_PACKAGES=ON`
  - Broad package enable; may require many additional TPLs.
- `-DTrilinos_ENABLE_<Package>=ON`
  - Targeted package enable for incremental or minimal builds.

### Build behavior flags
- `-GNinja`
  - Uses Ninja generator instead of Makefiles.
- `-DBUILD_SHARED_LIBS=ON`
  - Builds shared libraries (often reducing link time/binary size pressure).
- `-DTrilinos_ENABLE_TESTS=ON`
  - Enables tests for configured packages.

### Type/system support flags
- `-DTrilinos_ENABLE_FLOAT=ON`
  - Enables float scalar support.
- `-DTrilinos_ENABLE_COMPLEX=ON`
  - Enables `std::complex<T>` scalar support.
- `-DTrilinos_ENABLE_Fortran=OFF`
  - Disables Fortran support when Fortran toolchain is unavailable/not needed.

### Practical usage pattern
1. Start minimal: compiler + install prefix + selected packages.
2. Add MPI and TPL-related flags intentionally.
3. Enable tests after baseline configure/build succeeds.
4. Keep configure command in script form for repeatable iteration.

### Preset-era guidance
When using `CMakePresets.json`, treat these flags as the values that should live in preset `cacheVariables` instead of ad-hoc shell history.
Capture preset context in triage/escalation (`configure`/`build` preset names + any CI-only overrides) when failures occur.

### See also
- `30_CMAKE_CONFIGURE_SCRIPT_TEMPLATE_LIBRARY.md` — Ready-made configure script templates using these flags
- `40_BUILD_PROFILE_SELECTION_MATRIX.md` — Goal-to-profile matrix with concrete flag combinations
- `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md` — Detailed profile templates and flag rationale
- `58_CMAKE_PRESETS_ADOPTION_GUIDE.md` — Migrating these flags into `CMakePresets.json`
- `33_BUILD_AND_INSTALL_COMMAND_CHEATSHEET.md` — Copy/paste command bundles for common scenarios

## Validation
- Confirm flag behavior against active `INSTALL.rst` guidance.
- Confirm selected package flags map to valid package names from `PackagesList.cmake`.
- Cross-references validated against existing docs pack (2026-04-13).

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/PackagesList.cmake`
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
