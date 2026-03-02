# Trilinos Downstream Integration Failure Patterns

## Scope
Quick router for common failures when building a downstream CMake project against an installed Trilinos.

## Audience
- Engineers integrating Trilinos into external applications
- LLM agents answering “why can’t my app find/link Trilinos?” questions

## Symptom → probable cause → first fix

### 1) `Could not find TrilinosConfig.cmake`
**Probable cause**
- Trilinos install prefix is not on CMake search path.

**First fix**
- Set one of:
  - `-DTrilinos_DIR=<install-prefix>/lib/cmake/Trilinos` (or platform-specific equivalent)
  - `-DCMAKE_PREFIX_PATH=<install-prefix>`
- Re-run configure from a clean downstream build directory.

---

### 2) Downstream configure finds Trilinos, but target link fails with missing package libs
**Probable cause**
- Downstream app requests features/packages not enabled in installed Trilinos.

**First fix**
- Reconcile required package list with the Trilinos build configuration.
- Rebuild Trilinos with required `Trilinos_ENABLE_<Package>=ON` flags.

---

### 3) MPI symbol/link errors in downstream app
**Probable cause**
- Mixed MPI/non-MPI toolchains between Trilinos build and downstream build.

**First fix**
- Use MPI wrappers consistently (C/C++/Fortran as needed) in both Trilinos and downstream builds.
- Avoid mixing system compilers with wrapper-compiled libraries.

---

### 4) Runtime loader error (`lib*.so: cannot open shared object file`)
**Probable cause**
- Runtime linker path does not include Trilinos shared-library directories.

**First fix**
- Add install lib path to runtime linker environment (`LD_LIBRARY_PATH` on Linux) or use rpath settings during downstream build.

---

### 5) ABI/compiler mismatch behavior (strange crashes, unresolved symbols)
**Probable cause**
- Downstream and Trilinos compiled with incompatible compiler versions/flags/C++ standards.

**First fix**
- Align compiler family/version and major ABI-impacting flags.
- Rebuild one side to match the other when uncertain.

---

### 6) `find_package(Trilinos)` succeeds, but expected imported targets/variables differ
**Probable cause**
- Version drift across docs/examples vs installed Trilinos metadata.

**First fix**
- Inspect installed Trilinos CMake config files directly in the install tree.
- Base downstream logic on exported metadata from the installed version, not assumptions.

## Minimal downstream checklist
1. Confirm install prefix is correct and complete.
2. Configure downstream with explicit `Trilinos_DIR` or `CMAKE_PREFIX_PATH`.
3. Keep compiler/MPI toolchain consistent.
4. Verify required Trilinos packages were enabled at Trilinos build time.
5. Validate runtime library path/rpath for shared-library installs.

## Cross references
- `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `13_CMAKE_FLAG_QUICK_REFERENCE.md`
- `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`

## Provenance
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- Installed Trilinos CMake package metadata files (`TrilinosConfig*.cmake` in install tree)
