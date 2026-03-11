# Ad-hoc to CMake Preset Migration Checklist (Trilinos)

## Scope
Step-by-step checklist to migrate unstable ad-hoc Trilinos configure commands into reproducible CMake preset workflows.

## When to use
Use this when teams repeatedly share long one-off `cmake -D...` commands and local/CI drift keeps causing inconsistent failures.

## Migration checklist

### 1) Capture current known-good ad-hoc command
- Save the exact configure command currently used.
- Record compiler family, MPI mode, install prefix, and package scope.

### 2) Define baseline preset(s)
- Create one serial baseline preset and one MPI baseline preset.
- Lock generator, toolchain intent, install prefix convention, and minimal package scope.

### 3) Convert volatile `-D` flags into inherited child presets
- Keep parent preset small and stable.
- Move scenario-specific differences (debug/release, package expansion, CI tuning) into child presets.

### 4) Pair configure and build presets
- Ensure every configure preset has a matching build preset.
- Avoid “configure via preset, build via ad-hoc command” mixed mode.

### 5) Isolate build directories by preset family
- Do not reuse a single build tree across incompatible presets.
- Apply cache reset protocol when changing compiler/MPI/TPL roots.

### 6) Validate parity between local and CI
- Run same preset names in local and CI where possible.
- If CI needs extra behavior, encode it in explicit CI child presets.

### 7) Add failure routing hooks
- For preset failures, route immediately to `59_CMAKE_PRESETS_FAILURE_PATTERNS.md`.
- Capture diagnostics with `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md` before broad fixes.

## Definition of done
- Preset names are documented and discoverable with `cmake --list-presets`.
- Local and CI use preset-based configure/build paths.
- Known failure-handling route is documented for preset regressions.
- A parity check confirms local and CI configure intent is equivalent (except explicitly documented CI-only overrides).

## Quick parity guard (recommended)
After migrating, run this on local and CI for the same preset and compare outputs:
```bash
cmake --preset <configure-preset>
cmake -N -LA build/<preset-build-dir> \
  | grep -E 'CMAKE_(C|CXX|Fortran)_COMPILER|CMAKE_BUILD_TYPE|CMAKE_INSTALL_PREFIX|CMAKE_CXX_STANDARD|BUILD_SHARED_LIBS|Trilinos_ENABLE_|TPL_ENABLE_MPI|MPI_' \
  > preset-cache-signals.txt
```
Archive `preset-cache-signals.txt` as a lightweight parity artifact.

## Related docs
- `58_CMAKE_PRESETS_ADOPTION_GUIDE.md`
- `59_CMAKE_PRESETS_FAILURE_PATTERNS.md`
- `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md`
- `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`

## Provenance
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/sampleScripts/*`
- Official docs index: <https://trilinos.github.io/documentation.html>
