# Build/Install Log Capture and Minimal-Repro Template

## Scope
Provide a repeatable, low-noise template for capturing Trilinos configure/build/install failures and producing a minimal reproducible case that others can diagnose quickly.

## Audience
- Engineers debugging Trilinos configure/build/install failures
- LLM assistants preparing high-signal troubleshooting summaries

## Prerequisites
- A Trilinos source checkout
- A separate build directory (out-of-source build)
- Ability to re-run configure/build with explicit flags

## Content

### Why this page exists
Most troubleshooting delays come from incomplete context: missing CMake cache details, unknown compiler/MPI wrappers, or partial logs. This template standardizes what to capture first so triage can start immediately.

### Minimum context bundle (capture first)
Collect these artifacts for any failure report:
1. **System + toolchain identity**
   - `uname -a`
   - `cmake --version`
   - compiler versions (`cc --version`, `c++ --version` or wrapper equivalents)
   - MPI wrapper identity where relevant (`which mpicc`, `mpicc --version`)
2. **Configure invocation**
   - exact command or `do-configure` script used
   - whether build is MPI or non-MPI
   - package enable set (`Trilinos_ENABLE_<Package>=ON` and broad toggles)
3. **CMake cache snapshot**
   - `build/CMakeCache.txt`
4. **Primary logs**
   - `build/CMakeFiles/CMakeOutput.log`
   - `build/CMakeFiles/CMakeError.log`
   - failing build log section (target + first error)
5. **Install/runtime context (if post-build issue)**
   - install prefix
   - loader-path-related variables in use
   - exact runtime error text

### Fast log capture commands
```bash
# from build directory
cmake .. 2>&1 | tee configure.log
cmake --build . -j"$(nproc)" 2>&1 | tee build.log
cmake --install . 2>&1 | tee install.log
```

If configure fails early, still keep `configure.log`, `CMakeCache.txt` (if present), and `CMakeFiles/CMake*.log`.

### Minimal-repro reduction workflow
Use this order to reduce noise while preserving failure behavior:
1. Start from a **clean build directory**.
2. Use a **minimal package set** first (single target package or minimal known-good profile).
3. Disable optional features/TPLs not required for the failing path.
4. Reintroduce toggles one at a time until the failure reappears.
5. Record the **smallest flag set** that still reproduces.

### Report template (copy/paste)
```text
Failure stage: [configure|build|install|runtime]
Observed error (first decisive message):
  <paste exact message>

Environment:
  OS/kernel:
  CMake:
  Compiler C/C++:
  MPI wrappers (if used):

Configure command/script:
  <exact command or script body>

Key CMake options:
  Trilinos_ENABLE_*:
  TPL_*:
  BUILD_SHARED_LIBS:
  CMAKE_BUILD_TYPE:
  CMAKE_INSTALL_PREFIX:

Artifacts attached:
  - configure.log
  - build.log (or failing section)
  - install.log (if relevant)
  - CMakeCache.txt
  - CMakeOutput.log / CMakeError.log

Reduction status:
  Minimal package set that still fails:
  Last known working variant:
```

### Escalation guidance
Escalate after one full reduction pass if:
- failure persists with a minimal package profile,
- error points to compiler/MPI/TPL ABI mismatch that cannot be isolated locally,
- behavior diverges between CI-like settings and local settings with the same flags.

When escalating, include the template above and link to relevant troubleshooting pages for stage-based routing.

## Validation
- Template includes configure/build/install/runtime stages explicitly.
- Captured artifacts align with CMake/TriBITS troubleshooting expectations.
- Reduction workflow narrows variables before escalation.

## Provenance
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
- `library/Trilinos/18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`
- `library/Trilinos/36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- `library/Trilinos/39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`
- Trilinos upstream install/build docs: <https://github.com/trilinos/Trilinos>
- Official Trilinos documentation index: <https://trilinos.github.io/documentation.html>
