# Trilinos Troubleshooting Matrix (Configure and Build)

## Scope
Common configure/build/install failure patterns for Trilinos, with diagnosis and fix paths.

## Audience
- Engineers troubleshooting Trilinos builds
- LLM systems generating corrective build guidance

## Prerequisites
- Access to configure/build logs
- Access to the original configure command and selected CMake flags

## Content

## How to use this matrix
1. Match the observed error pattern to the closest row.
2. Run the listed quick checks.
3. Apply the shortest safe fix path.
4. Re-run configure/build and compare first failing lines.

### 1) Configure fails due to missing TPLs
- **Typical signal:** CMake reports missing third-party libraries after enabling many packages.
- **Likely cause:** `-DTrilinos_ENABLE_ALL_PACKAGES=ON` pulls package requirements not available on host.
- **Quick checks:**
  - Review CMake output listing missing dependencies.
  - Confirm whether all packages are required for the target use case.
- **Fix:**
  - Provide required TPL paths, or
  - switch to targeted package enables (`-DTrilinos_ENABLE_<Package>=ON`).

### 2) MPI build misconfiguration
- **Typical signal:** configure/build errors around MPI headers, libraries, or ABI mismatch.
- **Likely cause:** mixed toolchain (system compiler + MPI wrappers) or incorrect MPI base.
- **Quick checks:**
  - Confirm `TPL_ENABLE_MPI=ON` is intended.
  - Verify C/C++/Fortran compiler selection is consistent.
- **Fix:**
  - Use MPI wrapper compilers consistently (`mpicc`, `mpicxx`, optional `mpifort`) or set correct `MPI_BASE_DIR`.

### 3) Fortran compiler not found
- **Typical signal:** CMake fails Fortran compiler detection.
- **Likely cause:** Fortran compiler absent while selected package set implies Fortran support.
- **Quick checks:**
  - Determine whether Fortran is required for the selected package set.
- **Fix:**
  - Install/configure a Fortran compiler, or
  - disable Fortran via `-DTrilinos_ENABLE_Fortran=OFF`.

### 4) Overbroad package enable creates fragile builds
- **Typical signal:** very large configure/build surface with many unrelated failures.
- **Likely cause:** enabling all packages before establishing a baseline.
- **Quick checks:**
  - Confirm whether the immediate goal is quick usable install vs full ecosystem build.
- **Fix:**
  - Start with a minimal package set,
  - then add packages incrementally by family.

### 5) Slow linking or unstable large builds
- **Typical signal:** long link times, oversized binaries, memory pressure.
- **Likely cause:** static-heavy configuration with broad feature enable.
- **Quick checks:**
  - Confirm generator and shared/static library settings.
- **Fix:**
  - Use `-GNinja` where available.
  - Consider `-DBUILD_SHARED_LIBS=ON`.

### 6) Invalid or unclear package toggle names
- **Typical signal:** uncertainty about valid `Trilinos_ENABLE_<Package>` values.
- **Likely cause:** package names not validated against repository metadata.
- **Quick checks:**
  - Review configure output package summary.
  - Cross-check `PackagesList.cmake`.
- **Fix:**
  - Use exact package names as defined in source metadata.

### 7) Downstream app cannot find installed Trilinos
- **Typical signal:** `find_package(Trilinos)` fails in consumer project.
- **Likely cause:** install prefix/path not correctly exposed to downstream CMake.
- **Quick checks:**
  - Validate install prefix contains `TrilinosConfig.cmake`.
  - Verify downstream CMake search path settings.
- **Fix:**
  - Follow `demos/simpleBuildAgainstTrilinos/README.md` integration pattern.

## Validation
- Confirm fixes by re-running configure and build from a clean build directory when possible.
- Preserve first failing error block before and after change to verify issue resolution.

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- `Trilinos/PackagesList.cmake`
- `Trilinos/sampleScripts/*`
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- Official docs index: <https://trilinos.github.io/documentation.html>
