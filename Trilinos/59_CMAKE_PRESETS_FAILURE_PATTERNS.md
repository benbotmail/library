# CMake Presets Failure Patterns (Trilinos)

## Scope
Targeted troubleshooting for failures introduced by preset-driven Trilinos workflows (`CMakePresets.json`), especially when local and CI behavior diverge.

## When to use
Use this page when a preset-based run fails and non-preset command guidance is too generic.

## Symptom → likely cause → first fix

### 1) `cmake --preset <name>` says preset not found
- **Likely cause:** wrong working directory or preset file not present at repo root.
- **First fix:** run `cmake --list-presets` from the directory containing `CMakePresets.json`.

### 2) Preset resolves, but compiler path is invalid
- **Likely cause:** hardcoded compiler path no longer exists on host/runner.
- **First fix:** validate compiler paths in preset cache variables and align with active toolchain install.

### 3) Local passes, CI fails with different options
- **Likely cause:** CI injects extra flags/environment overrides not represented in presets.
- **First fix:** move CI-specific `-D` overrides into an inherited CI preset and keep local/CI deltas explicit.

### 4) Reusing one build dir across incompatible presets
- **Likely cause:** stale cache from preset A contaminates preset B.
- **First fix:** isolate build directories per preset family; apply cache reset protocol before switching major toolchain/MPI settings.

### 5) MPI preset fails on one machine only
- **Likely cause:** wrapper/compiler mismatch or machine-specific MPI root.
- **First fix:** verify wrapper identity (`mpicc/mpicxx`) and ensure MPI-related preset values are host-appropriate.

### 6) Preset build succeeds, downstream app link/runtime fails
- **Likely cause:** install prefix assumptions differ from downstream `Trilinos_DIR` or runtime loader paths.
- **First fix:** verify `CMAKE_INSTALL_PREFIX`, `TrilinosConfig.cmake` location, and downstream `Trilinos_DIR` wiring.

### 7) Preset works for one user but not another on same machine
- **Likely cause:** hidden user-level preset overlays (`CMakeUserPresets.json`) or shell env differences.
- **First fix:** compare effective presets and env; move required settings into project presets if they are team/CI relevant.

## Fast verification commands
```bash
cmake --list-presets
cmake --preset <configure-preset>
cmake --build --preset <build-preset>

# Optional: reveal configure decision trace for preset runs
cmake --preset <configure-preset> --trace-expand --debug-output 2>&1 | tee preset-configure-trace.log
```

If failure persists, collect bundle:
- `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md`

## Deterministic triage order (preset failures)
1. Confirm preset visibility and names (`cmake --list-presets`).
2. Confirm correct working directory and presence of `CMakePresets.json`.
3. Validate compiler/MPI paths encoded in the selected preset.
4. Enforce build-dir isolation per preset family (no incompatible reuse).
5. Re-run with log capture and route by first decisive error.

## Related docs
- `58_CMAKE_PRESETS_ADOPTION_GUIDE.md`
- `60_ADHOC_TO_PRESET_MIGRATION_CHECKLIST.md`
- `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`
- `44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md`
- `27_DOWNSTREAM_INTEGRATION_FAILURE_PATTERNS.md`

## Provenance
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/sampleScripts/*`
- Official docs index: <https://trilinos.github.io/documentation.html>
