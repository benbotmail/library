# Trilinos Build Profiles (Minimal to Advanced)

## Scope
Practical build profile templates for progressively complex Trilinos configurations.

## Audience
- Engineers choosing a starting Trilinos build shape
- LLM systems recommending build profiles based on user constraints

## Prerequisites
- CMake 3.23+
- Working C/C++ toolchain
- Optional MPI and accelerator toolchains

## Content

### Profile A: Minimal CPU baseline
Use when you need a first successful build with low dependency risk.

```bash
cmake \
  -DCMAKE_C_COMPILER=<cc> \
  -DCMAKE_CXX_COMPILER=<cxx> \
  -DTrilinos_ENABLE_Teuchos=ON \
  -DTrilinos_ENABLE_Tpetra=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <trilinos-src>
```

### Profile B: MPI-enabled baseline
Use for distributed-memory runs with moderate complexity.

```bash
cmake \
  -DTPL_ENABLE_MPI=ON \
  -DMPI_BASE_DIR=<mpi-root> \
  -DTrilinos_ENABLE_Teuchos=ON \
  -DTrilinos_ENABLE_Tpetra=ON \
  -DTrilinos_ENABLE_Belos=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <trilinos-src>
```

### Profile C: Solver-oriented stack
Use when iterative/direct solver workflows are the primary goal.

```bash
cmake \
  -DTPL_ENABLE_MPI=ON \
  -DTrilinos_ENABLE_Tpetra=ON \
  -DTrilinos_ENABLE_Belos=ON \
  -DTrilinos_ENABLE_Ifpack2=ON \
  -DTrilinos_ENABLE_Amesos2=ON \
  -DTrilinos_ENABLE_MueLu=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <trilinos-src>
```

### Profile D: Broad package enable (high dependency surface)
Use only when you intentionally want wide package coverage.

```bash
cmake \
  -DTPL_ENABLE_MPI=ON \
  -DTrilinos_ENABLE_ALL_PACKAGES=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <trilinos-src>
```

### Profile selection guidance
- Start with A/B to validate toolchain and dependency basics.
- Move to C for solver-heavy workloads.
- Use D only after environment/TPL readiness is confirmed.

## Validation
- For each profile, run configure first and inspect missing TPL output before building.
- Preserve profile-specific configure scripts for repeatability.
- Validate installation via downstream integration (`07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`).

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/PackagesList.cmake`
- `Trilinos/TPLsList.cmake`
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
