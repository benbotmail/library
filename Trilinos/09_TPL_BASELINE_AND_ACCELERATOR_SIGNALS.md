# Trilinos TPL Baseline and Accelerator Signals

## Scope
Quick-reference guide to third-party library (TPL) baseline expectations and accelerator-related TPL signals in Trilinos metadata.

## Audience
- Engineers preparing environment/toolchain prerequisites before configuring Trilinos
- LLM systems answering dependency-precheck and environment-readiness questions

## Prerequisites
- Access to `TPLsList.cmake` and `INSTALL.rst`
- Familiarity with CMake configure flags (`TPL_ENABLE_*`, package enables)

## Content

### Baseline configure prerequisites from install guidance
From `INSTALL.rst`, baseline prerequisites include:
- CMake 3.23.0 or newer
- C and C++ compilers
- Optional Fortran compiler
- Optional MPI install

### Why TPL planning matters
Enabling broad package sets (for example `Trilinos_ENABLE_ALL_PACKAGES=ON`) can activate many TPL requirements. Missing TPLs are a common configure-time failure source.

### High-signal TPL groups from `TPLsList.cmake`

#### Core numeric and runtime foundations
- `BLAS`, `LAPACK`
- `MPI`
- `Pthread`
- `Boost`, `BoostLib`
- `Zlib`, `HDF5`, `Netcdf`, `Pnetcdf`, `CGNS`

#### GPU and accelerator-adjacent signals
- NVIDIA stack signals: `CUDA`, `CUBLAS`, `CUSOLVER`, `CUSPARSE`
- AMD ROCm signals: `ROCBLAS`, `ROCSPARSE`, `ROCSOLVER`
- Additional accelerator/perf-related signals: `AmgX`, `VTune`, `PAPI`, `TBB`

#### Solver ecosystem signals (selected)
- `ParMETIS`, `SuperLU`, `SuperLUDist`, `MUMPS`, `SCALAPACK`, `PETSC`, `HYPRE`

### TriBITS tier markers in TPL metadata
`TPLsList.cmake` includes per-TPL markers such as `PT`, `ST`, `EX` (and others in-file). Treat these as build metadata signals, not standalone compatibility guarantees.

### Practical pre-configure checklist
1. Confirm baseline compilers and CMake version.
2. Decide MPI vs non-MPI configure path.
3. Decide targeted package set before enabling all packages.
4. Pre-verify presence/paths for expected TPL family (core numeric, I/O, solver, accelerator).
5. Keep configure command in a script to iterate quickly when TPL resolution fails.

### See also
- `31_TPL_DISCOVERY_AND_PATH_HINTS.md` — Practical path-hint and triage guide for missing TPLs
- `17_PACKAGE_SELECTION_STRATEGY.md` — Framework for choosing package sets (drives TPL requirements)
- `13_CMAKE_FLAG_QUICK_REFERENCE.md` — CMake flag reference including TPL enable flags
- `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md` — Configure failure patterns including missing TPLs
- `55_KNOWN_GOOD_STARTER_CONFIGS.md` — Conservative starter configs with minimal TPL requirements

## Validation
- Re-check TPL names and markers directly in `TPLsList.cmake` for the active Trilinos revision.
- Cross-check package/TPL implications with configure output in the current build tree.
- Cross-references validated against existing docs pack (2026-04-13).

## Provenance
- `Trilinos/TPLsList.cmake`
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- Official docs index: <https://trilinos.github.io/documentation.html>
