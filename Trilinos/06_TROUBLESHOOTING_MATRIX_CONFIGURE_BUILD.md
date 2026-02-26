# Trilinos Troubleshooting Matrix (Configure/Build)

## Scope
High-frequency failure modes during configure/build/install, with fast diagnosis paths.

## How to use this page
1. Match error pattern to a row.
2. Apply quick checks in order.
3. Re-run configure/build and capture first failing lines.

## Matrix

### 1) Configure fails due to missing TPLs
- **Typical signal:** CMake reports missing third-party libraries after enabling many packages.
- **Likely cause:** `-DTrilinos_ENABLE_ALL_PACKAGES=ON` pulls package requirements not available on host.
- **Quick checks:**
  - Review CMake output section listing missing dependencies.
  - Confirm whether all packages are actually needed.
- **Fix:**
  - Provide required TPL paths, or
  - Switch to targeted package enables (`-DTrilinos_ENABLE_<Package>=ON`) first.

### 2) MPI build misconfiguration
- **Typical signal:** configure/build errors around MPI headers/libs or ABI mismatch.
- **Likely cause:** mixed toolchain (system compiler + MPI wrappers) or wrong MPI base.
- **Quick checks:**
  - Ensure `TPL_ENABLE_MPI=ON` is intentional.
  - Verify C/C++/Fortran compiler selection is consistent.
- **Fix:**
  - Use MPI wrapper compilers consistently (`mpicc/mpicxx/mpifort`) or a correct `MPI_BASE_DIR`.

### 3) Fortran compiler not found / Fortran-related configure errors
- **Typical signal:** CMake failure for Fortran detection.
- **Likely cause:** Fortran compiler absent while Fortran-enabled packages are implied.
- **Quick checks:**
  - Is Fortran required for your chosen package set?
- **Fix:**
  - Install/configure Fortran compiler, or
  - disable Fortran: `-DTrilinos_ENABLE_Fortran=OFF`.

### 4) Overbroad package enable causes long or fragile builds
- **Typical signal:** huge configure/build surface; many unrelated failures.
- **Likely cause:** trying all packages before establishing baseline.
- **Quick checks:**
  - Confirm baseline goal (quick usable install vs full ecosystem build).
- **Fix:**
  - Start with a minimal/focused package set,
  - then incrementally add packages.

### 5) Slow or brittle link/build behavior on large builds
- **Typical signal:** long link times, oversized binaries, memory pressure.
- **Likely cause:** static-heavy default and broad feature enable.
- **Quick checks:**
  - Generator and build mode choices.
- **Fix:**
  - Consider `-GNinja`.
  - Consider `-DBUILD_SHARED_LIBS=ON`.

### 6) Unclear what package names/options are valid
- **Typical signal:** confusion on valid package toggles.
- **Likely cause:** package naming not verified from source metadata.
- **Quick checks:**
  - Use configure output package summary.
  - Cross-check `PackagesList.cmake`.
- **Fix:**
  - Use exact package names with `Trilinos_ENABLE_<Package>=ON`.

### 7) Downstream app cannot find installed Trilinos
- **Typical signal:** `find_package(Trilinos)` fails in consumer project.
- **Likely cause:** install layout/path not wired into downstream CMake context.
- **Quick checks:**
  - Validate install prefix contents.
  - Check downstream CMake path variables.
- **Fix:**
  - Follow `demos/simpleBuildAgainstTrilinos/README.md` pattern.

## Practical debug procedure (repeatable)
1. Save configure command in a script (`do-configure`) for reproducibility.
2. Start from minimal package set.
3. Enable tests only when baseline configure/build succeeds.
4. Add packages one cluster at a time.
5. Keep first failing error block when escalating/reporting.

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- `Trilinos/PackagesList.cmake`
- `Trilinos/sampleScripts/*` (platform-specific examples)
- Official docs index: <https://trilinos.github.io/documentation.html>

## Status
Draft v1 (next pass: attach package-specific failure signatures per major family).
