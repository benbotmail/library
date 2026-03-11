# Trilinos Platform Baseline and Toolchain Minimums

## Scope
A conservative baseline for first-attempt Trilinos configure/build success, focused on avoiding mixed-toolchain and missing-compiler failures.

## When to use this page
Use this before the first configure when you want to minimize avoidable failures.

## Baseline checklist (pre-configure)

### 1) Choose one compiler family and stick to it
- Keep `CC`, `CXX`, and (if needed) `FC` in the same family/toolchain.
- Avoid mixing compiler families across configure/build/downstream stages.

### 2) Decide MPI vs non-MPI up front
- **Non-MPI path:** keep MPI disabled and use normal host compilers.
- **MPI path:** use MPI wrapper compilers consistently (`mpicc`, `mpicxx`, optional `mpifort`) or provide a consistent MPI root/path.

### 3) Confirm generator + parallel build strategy
- Prefer Ninja where available for faster incremental builds.
- If using Makefiles, set a conservative parallelism level first and increase after baseline success.

### 4) Start with a narrow package set
- Do **not** begin with broad “all packages” enables.
- Enable only packages needed for your immediate use case, then expand incrementally.

### 5) Align language standard and downstream expectations
- Set an explicit C++ standard if your toolchain or downstream stack requires it.
- Keep the same standard and compiler family in downstream apps linking against Trilinos.

### 6) Verify install-prefix intent early
- Decide install prefix before configure.
- Ensure downstream CMake lookup strategy for `TrilinosConfig.cmake` is clear before integration testing.

### 7) Preset preflight (if using `CMakePresets.json`)
- Confirm preset names from repo root with `cmake --list-presets`.
- Ensure preset-selected compiler/MPI intent matches your chosen baseline path.
- Avoid hidden divergence from `CMakeUserPresets.json` unless intentionally documented.

## Minimal first-success command pattern (shape, not a full preset)
```bash
cmake -S <trilinos-src> -B <build-dir> \
  -G Ninja \
  -D CMAKE_BUILD_TYPE=Release \
  -D CMAKE_INSTALL_PREFIX=<install-prefix> \
  -D BUILD_SHARED_LIBS=ON \
  -D Trilinos_ENABLE_TESTS=OFF \
  -D Trilinos_ENABLE_EXAMPLES=OFF \
  -D Trilinos_ENABLE_<NeededPackage>=ON
```

Then:
```bash
cmake --build <build-dir> -j
cmake --install <build-dir>
```

## Fast failure triage if baseline still fails
1. Capture the **first** real configure error block (not the final summary line).
2. Route by stage:
   - configure detection failures → `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
   - toolchain/MPI consistency issues → `44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md`
   - compiler/C++ standard mismatch → `46_COMPILER_AND_CXX_STANDARD_ALIGNMENT_GUIDE.md`
   - stale cache suspicion → `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`
3. If unresolved, prepare escalation packet: `45_BUILD_INSTALL_ESCALATION_HANDOFF_CHECKLIST.md`.

## Related docs
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `35_PRECONFIGURE_ENVIRONMENT_CHECKLIST.md`
- `40_BUILD_PROFILE_SELECTION_MATRIX.md`
- `55_KNOWN_GOOD_STARTER_CONFIGS.md`

## Provenance
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/PackagesList.cmake`
- `Trilinos/sampleScripts/*`
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- Official docs index: <https://trilinos.github.io/documentation.html>
