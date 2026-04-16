# Trilinos Terms and Metadata Glossary

## Scope
Glossary of recurring Trilinos and TriBITS terms used across this docs pack.

## Audience
- Engineers new to Trilinos naming and build metadata
- LLM systems normalizing terminology across retrieval results

## Prerequisites
- Basic familiarity with CMake-based builds
- Access to Trilinos metadata files (`PackagesList.cmake`, `TPLsList.cmake`)

## Content

### Core project terms
- **Trilinos**: A package-oriented scientific computing framework targeting large-scale multiphysics and HPC workloads.
- **Package**: A modular Trilinos subsystem (for example `Tpetra`, `Teuchos`, `Belos`).
- **TPL (Third-Party Library)**: External dependency integrated through TriBITS/CMake metadata.

### Build-system terms
- **TriBITS**: The build/integration layer used by Trilinos for packages, dependencies, and enable/disable logic.
- **`Trilinos_ENABLE_<Package>=ON`**: CMake flag pattern for enabling specific Trilinos packages.
- **`TPL_ENABLE_<Name>=ON`**: CMake flag pattern for enabling or requiring a named TPL.

### Metadata marker terms
- **PT / ST / EX**: Tier-like metadata markers appearing in package/TPL definitions.
- **TS / SS**: Additional marker codes present in TPL metadata.

These markers are useful routing/build metadata signals. Treat them as internal classification data, not standalone compatibility guarantees.

### Practical dependency terms
- **Baseline prerequisites**: CMake + C/C++ compilers, with optional Fortran and MPI depending on configuration.
- **Accelerator signals**: TPL entries indicating GPU/accelerator stacks (for example CUDA/ROCm-related entries).
- **Downstream integration**: Building a separate application against installed Trilinos using `find_package(Trilinos)`.

### Troubleshooting terms
- **Missing TPL failure**: Configure-time error caused by unresolved third-party dependencies.
- **Toolchain mismatch**: Inconsistent compiler or MPI wrapper selection between Trilinos build and downstream/app build.
- **Install prefix**: Path where Trilinos is installed and where CMake package config files are expected.
- **RPATH**: Runtime library search path embedded in executables/shared libraries; critical for correct loader behavior.
- **ABI compatibility**: Binary interface consistency required when mixing shared libraries built with different compilers/settings.
- **CMake cache staleness**: Old variable values in `CMakeCache.txt` that persist across reconfigure attempts.

### CMake and build terms
- **Configure stage**: CMake generates build system; detects dependencies and sets options.
- **Build stage**: Compilation and linking of Trilinos libraries from configured sources.
- **Install stage**: Copying of headers, libraries, and CMake package files to the install prefix.
- **Clean reconfigure**: Deleting `CMakeCache.txt` and `CMakeFiles/` before re-running cmake.

### See also
- `02_SOURCE_OF_TRUTH_MAP.md` — Authority order for Trilinos sources when claims conflict
- `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md` — TPL dependency and accelerator signal details
- `03_PACKAGE_CATALOG.md` — Package directory with CODEOWNERS mapping
- `05_PACKAGE_ROUTING_AND_TIERS.md` — Package-family routing and tier classification
- `01_DOCUMENTATION_CONVENTIONS.md` — Documentation standards for this pack

## Validation
- Re-check marker and term usage against active revision metadata files.
- Keep glossary definitions aligned with current docs in this collection.
- Cross-references validated against existing docs pack (2026-04-13).

## Provenance
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/PackagesList.cmake`
- `Trilinos/TPLsList.cmake`
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
