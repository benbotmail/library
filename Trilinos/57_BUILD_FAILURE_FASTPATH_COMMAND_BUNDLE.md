# Trilinos Build Failure Fastpath Command Bundle

## Scope
A compact command bundle to collect high-signal diagnostics for Trilinos configure/build/install failures before deep triage.

## When to use
Use immediately after a failed configure/build/install run to capture reproducible context and avoid back-and-forth.

## Output goals
Capture:
- toolchain identity
- generator/build metadata
- first failing log signals
- cache and install-path state

## Fastpath bundle
Run from your build directory unless noted.

### 1) Toolchain and environment identity
```bash
uname -a
cmake --version
${CXX:-c++} --version || true
${CC:-cc} --version || true
which cmake c++ cc || true
env | grep -E '^(CC|CXX|FC|CFLAGS|CXXFLAGS|LDFLAGS|PATH|LD_LIBRARY_PATH|CMAKE_PREFIX_PATH|PKG_CONFIG_PATH)=' || true
```

### 2) Configure command and cache snapshot
```bash
# If you used CMake presets:
cmake --list-presets || true

# Cache highlights (trim to high-signal keys)
cmake -N -LA . | grep -E 'CMAKE_(C|CXX|Fortran)_COMPILER|CMAKE_BUILD_TYPE|CMAKE_INSTALL_PREFIX|CMAKE_CXX_STANDARD|BUILD_SHARED_LIBS|Trilinos_ENABLE_|TPL_ENABLE_MPI|MPI_' || true
```

### 3) Re-run configure with full log capture
```bash
cmake -S <trilinos-src> -B . 2>&1 | tee configure.log
```

Then extract first errors:
```bash
grep -nE 'CMake Error|error:|undefined reference|cannot find|No such file|not found|failed' configure.log | head -n 40
```

### 4) Build log capture (if configure succeeded)
```bash
cmake --build . -j 2>&1 | tee build.log
```

Extract first errors:
```bash
grep -nE 'error:|undefined reference|cannot find|No such file|collect2: error|ld:|ninja: build stopped' build.log | head -n 60
```

### 5) Install and runtime signal checks (if build succeeded)
```bash
cmake --install . 2>&1 | tee install.log
find "${CMAKE_INSTALL_PREFIX:-<install-prefix>}" -name 'TrilinosConfig.cmake' 2>/dev/null | head
```

Optional loader check for a failing downstream binary:
```bash
ldd <downstream-binary> | grep -E 'not found|trilinos|mpi' || true
```

## Packaging for escalation
Include these files/snippets in handoff:
- `configure.log` (or first 200 lines around first error)
- `build.log` first error block
- key cache lines from step 2
- exact configure command used
- platform/toolchain outputs from step 1

### Optional: single-file evidence bundle
```bash
mkdir -p triage-bundle
cp -f configure.log build.log install.log triage-bundle/ 2>/dev/null || true
cp -f CMakeCache.txt triage-bundle/ 2>/dev/null || true
cp -f CMakeFiles/CMakeError.log CMakeFiles/CMakeOutput.log triage-bundle/ 2>/dev/null || true
printf '%s\n' "$(cmake --version | head -n1)" > triage-bundle/cmake-version.txt
printf '%s\n' "CC=${CC:-cc} CXX=${CXX:-c++}" > triage-bundle/compiler-env.txt
tar -czf trilinos-triage-bundle.tgz triage-bundle
```
Use `trilinos-triage-bundle.tgz` as the default attachment for escalations.

## Routing after capture
- Configure detection/TPL issues → `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- Preset-specific failures / local-vs-CI preset divergence → `59_CMAKE_PRESETS_FAILURE_PATTERNS.md`
- MPI/toolchain mismatch → `44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md`
- Compiler/C++ standard mismatch → `46_COMPILER_AND_CXX_STANDARD_ALIGNMENT_GUIDE.md`
- Runtime loader/RPATH failures → `52_RUNTIME_LOADER_RPATH_TRIAGE.md`
- Full escalation packet → `45_BUILD_INSTALL_ESCALATION_HANDOFF_CHECKLIST.md`

## Provenance
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/sampleScripts/*`
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- Official docs index: <https://trilinos.github.io/documentation.html>
