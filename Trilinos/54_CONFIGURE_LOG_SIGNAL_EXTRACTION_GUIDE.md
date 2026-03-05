# Configure Log Signal Extraction Guide (Trilinos)

## Purpose
Speed up configure-stage triage by extracting high-signal lines from large CMake logs and mapping them to the next diagnostic action.

## When to use
Use this page when `cmake` configure fails and raw output is too long/noisy to route quickly.

## High-signal targets
Prioritize lines containing:
- `Could NOT find`
- `No package '...'
- `cannot find -l...`
- `CMAKE_<LANG>_COMPILER`
- `MPI` wrapper/tool detection
- `TriBITS` package disable reasons
- `Policy CMP` warnings that become errors

## Minimal extraction workflow
```bash
# 1) Capture full configure output
cmake -S ../Trilinos -B . [flags...] 2>&1 | tee configure.log

# 2) Extract likely-failure signals
grep -En "Could NOT find|No package '|cannot find -l|CMAKE_[A-Z_]*COMPILER|MPI|TriBITS|Policy CMP|error:" configure.log > configure-signals.log

# 3) Read CMake error context
test -f CMakeFiles/CMakeError.log && sed -n '1,220p' CMakeFiles/CMakeError.log

# 4) Read CMake output context
test -f CMakeFiles/CMakeOutput.log && sed -n '1,220p' CMakeFiles/CMakeOutput.log
```

## Signal → next action router
- **`Could NOT find <TPL>`**
  - Verify `<TPL>_DIR`/`CMAKE_PREFIX_PATH`; consult TPL path-hint guide.
- **Compiler ID or test compile failures**
  - Validate compiler family and C++ standard alignment; reset cache and reconfigure.
- **MPI found inconsistently (headers vs wrappers)**
  - Enforce coherent MPI wrappers/launcher; validate wrapper versions.
- **TriBITS package auto-disable surprises**
  - Check package dependency chain and explicitly set package enables/disables.
- **`cannot find -l<name>` at configure test stage**
  - Confirm library path visibility and shared/static consistency.
- **Policy warnings promoted to hard failures**
  - Pin compatible CMake version or set policy handling intentionally.

## What to include in first escalation message
- Exact failing configure command
- `configure-signals.log`
- First relevant block from `CMakeError.log`
- Toolchain identity (`which` + `--version` for compilers/MPI wrappers)
- Intended Trilinos/TPL prefixes

## Anti-patterns to avoid
- Posting entire multi-MB logs before extracting key failures
- Mixing old and new configure runs in one build directory
- Applying random flag changes without preserving the previous failing command

## Related docs
- `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- `31_TPL_DISCOVERY_AND_PATH_HINTS.md`
- `44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md`
- `46_COMPILER_AND_CXX_STANDARD_ALIGNMENT_GUIDE.md`
- `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`
- `43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md`

## Provenance notes
- Derived from recurring configure triage patterns in this Trilinos local docs pack.
- Uses standard CMake artifacts (`configure.log`, `CMakeError.log`, `CMakeOutput.log`) and common Linux text-filter workflow (`grep`).
