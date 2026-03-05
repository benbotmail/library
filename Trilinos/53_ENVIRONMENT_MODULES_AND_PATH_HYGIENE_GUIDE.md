# Environment Modules & Path Hygiene Guide (Trilinos Build/Install)

## Purpose
Provide a deterministic checklist for avoiding and triaging build/install failures caused by mixed environment modules, shadowed toolchains, and stale path variables.

## When to use this page
Use this guide when configure/build behavior is inconsistent across shells, CI jobs, or nodes, especially when symptoms suggest the wrong compiler, MPI, or TPL is being picked.

## High-signal symptoms
- `CMAKE_<LANG>_COMPILER` resolves to a different compiler than expected.
- `find_package(MPI)` finds headers/libs from a different MPI than wrapper compilers.
- Same CMake command succeeds in one shell and fails in another.
- `ldd` shows libraries resolved from an unexpected prefix.
- Rebuild after cache reset still reproduces with unexplained toolchain drift.

## Fast diagnosis sequence
1. **Capture active module stack (if using environment modules).**
2. **Record compiler/wrapper identity from PATH resolution.**
3. **Snapshot key environment variables affecting CMake discovery.**
4. **Confirm intended Trilinos/TPL prefixes and remove competing paths.**
5. **Reconfigure from clean cache with explicit hints.**

## Minimal command bundle (Linux)
```bash
# 1) Module state (if module command exists)
module list 2>&1 || true

# 2) PATH-resolution for toolchain + MPI wrappers
which cmake gcc g++ cc c++ mpicc mpicxx mpirun 2>/dev/null || true

# 3) Version identity
cmake --version
gcc --version 2>/dev/null || true
g++ --version 2>/dev/null || true
mpicxx --version 2>/dev/null || true
mpirun --version 2>/dev/null || true

# 4) Environment snapshot
env | grep -E '^(PATH|LD_LIBRARY_PATH|LIBRARY_PATH|CPATH|CMAKE_PREFIX_PATH|PKG_CONFIG_PATH|CC|CXX|FC|OMPI_|I_MPI_)=' | sort
```

## Path hygiene rules
- Prefer **one coherent toolchain** (compiler + MPI + TPL stack) per build directory.
- Avoid mixing module-provided MPI with system compiler wrappers unless explicitly validated.
- Keep `CMAKE_PREFIX_PATH` minimal and intentional; remove legacy prefixes.
- Treat `LD_LIBRARY_PATH` as a temporary diagnostic tool, not a permanent fix for wrong build configuration.
- Use separate build directories for distinct toolchains/profiles.

## Clean-room reconfigure protocol (recommended)
1. Start a fresh shell (or unload all modules).
2. Load only the intended compiler/MPI modules.
3. Verify wrapper/compiler identity with `which` and `--version`.
4. Remove stale cache/build directory.
5. Re-run configure with explicit compilers and prefix hints.

Example:
```bash
cmake -S ../Trilinos -B . \
  -DCMAKE_C_COMPILER=mpicc \
  -DCMAKE_CXX_COMPILER=mpicxx \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_PREFIX_PATH="/opt/intended/tpls" \
  -DTrilinos_ENABLE_TESTS=OFF
```

## Common failure patterns → likely cause
- Compiler shown by `which` differs from cache value → stale cache or changed module stack after first configure.
- `mpicxx` points to MPI A, `mpirun` from MPI B → mixed runtime/launcher environment.
- TPL found from old prefix despite explicit new one → competing entries in `CMAKE_PREFIX_PATH`/module-provided variables.
- Runtime loader resolves wrong `.so` path → environment/path shadowing plus missing/incorrect RPATH policy.

## Escalation packet additions
Include these alongside normal build logs:
- `module list` output
- `which` output for compiler/MPI tools
- relevant env snapshot (`PATH`, `CMAKE_PREFIX_PATH`, `LD_LIBRARY_PATH`, `CC/CXX`)
- exact configure command used after clean-room reset

## Related docs
- `35_PRECONFIGURE_ENVIRONMENT_CHECKLIST.md`
- `44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md`
- `46_COMPILER_AND_CXX_STANDARD_ALIGNMENT_GUIDE.md`
- `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`
- `52_RUNTIME_LOADER_RPATH_TRIAGE.md`

## Provenance notes
- Aligns with Trilinos build guidance emphasizing coherent toolchain selection and explicit CMake configuration.
- Consolidates recurring triage practices from existing local docs in this pack:
  - preconfigure checks,
  - MPI wrapper/runtime consistency,
  - compiler/ABI alignment,
  - cache reset and reconfigure discipline,
  - runtime loader path triage.
- Command examples use standard Linux shell/module introspection conventions.
