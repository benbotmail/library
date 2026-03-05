# Runtime Loader & RPATH Triage (Trilinos + Downstream)

## Purpose
A fast, deterministic playbook for post-build failures where binaries compile/link successfully but fail at runtime with loader errors (`lib*.so: cannot open shared object file`, undefined symbols on startup, or wrong library picked at runtime).

## Typical symptoms
- `error while loading shared libraries: libtrilinos*.so: cannot open shared object file`
- `symbol lookup error: ... undefined symbol: ...`
- App runs on build host but fails on another machine/container
- MPI executable launches but crashes immediately before main logic

## Decision order (highest ROI first)
1. **Confirm install layout exists**
   - Verify `${CMAKE_INSTALL_PREFIX}/lib` (or `lib64`) contains expected Trilinos shared libs.
2. **Inspect runtime dependencies of failing executable**
   - `ldd <exe>` (Linux) to identify `not found` or unexpected paths.
3. **Check embedded RPATH/RUNPATH**
   - `readelf -d <exe> | grep -E 'RPATH|RUNPATH'`
4. **Compare build-time vs runtime library selection**
   - Confirm loader resolves to the same Trilinos/TPL stack intended at configure time.
5. **Fix at build-system level (preferred), not ad-hoc shell exports**
   - Set explicit install/build RPATH strategy in CMake and rebuild.

## Minimal triage command set (Linux)
```bash
# 1) What does the loader see?
ldd ./my_app

# 2) What RPATH/RUNPATH is embedded?
readelf -d ./my_app | grep -E 'RPATH|RUNPATH'

# 3) Which symbol is missing and where should it come from?
nm -D /path/to/suspect/lib.so | grep <symbol_fragment>

# 4) Deep trace (noisy but decisive)
LD_DEBUG=libs ./my_app 2>&1 | tee loader-trace.log
```

## Common root causes and fixes

### A) Missing Trilinos install path at runtime
**Signal:** `ldd` shows `libtrilinos*.so => not found`.

**Preferred fix:** embed install RPATH during downstream configure.
```cmake
set(CMAKE_BUILD_RPATH_USE_ORIGIN ON)
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH ON)
# Add explicit install lib dir(s) when needed:
list(APPEND CMAKE_INSTALL_RPATH "${TRILINOS_INSTALL_PREFIX}/lib")
```

### B) Wrong library version selected first (path shadowing)
**Signal:** `ldd` resolves Trilinos/TPL libs from unexpected system or legacy prefix.

**Fix:**
- Ensure a single intended Trilinos prefix in downstream `find_package(Trilinos ...)` hints.
- Remove conflicting prefixes from environment/module stack.
- Reconfigure from clean cache (see reset protocol) and rebuild.

### C) ABI/compiler-family mismatch surfaces at runtime
**Signal:** unresolved C++ symbols (`GLIBCXX_*`, mangled symbol mismatches), or startup crashes after successful link.

**Fix:**
- Align compiler family and C++ standard across Trilinos, TPLs, and downstream app.
- Rebuild inconsistent components as one coherent toolchain set.

### D) Shared/static inconsistency across stack
**Signal:** duplicate/undefined symbols, brittle link behavior, runtime instability.

**Fix:**
- Keep linkage mode consistent (all-shared or carefully-managed mixed mode).
- Rebuild downstream against the same linkage mode as installed Trilinos.

### E) MPI launcher/runtime mismatch
**Signal:** binary links, but `mpirun` launch fails immediately or picks wrong runtime libs.

**Fix:**
- Ensure MPI wrapper/compiler/launcher come from same MPI installation.
- Rebuild if wrappers and runtime are from different vendors/versions.

## Fast recovery pattern
1. Capture `ldd`, `readelf -d`, exact failing command, and full stderr.
2. Verify no stale CMake cache or mixed prefixes.
3. Reconfigure downstream with explicit Trilinos prefix + RPATH policy.
4. Rebuild and re-run dependency checks before retest.
5. If unresolved, escalate with a minimal repro bundle.

## Escalation packet (minimum)
- Failing executable + command line
- `ldd` output
- `readelf -d` output (RPATH/RUNPATH)
- Compiler + MPI wrapper versions
- Trilinos install prefix and linkage mode (shared/static)
- Clean configure command used for downstream app

## Routing
- Loader `not found` / path issues → this page + install/runtime router
- ABI/compiler inconsistency → compiler/C++ alignment + MPI/ABI checklist
- Mixed shared/static symptoms → linkage consistency guide

## Provenance notes
- Consolidates and sharpens runtime-loader triage from:
  - `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
  - `39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`
  - `44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md`
  - `46_COMPILER_AND_CXX_STANDARD_ALIGNMENT_GUIDE.md`
  - `51_SHARED_STATIC_LINKAGE_CONSISTENCY_GUIDE.md`
- Command semantics (`ldd`, `readelf`, `LD_DEBUG`) follow standard Linux ELF loader tooling behavior.
