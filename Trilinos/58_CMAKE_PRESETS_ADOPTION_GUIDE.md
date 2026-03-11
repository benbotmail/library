# Trilinos CMake Presets Adoption Guide

## Scope
Practical guidance for introducing CMake Presets into Trilinos build workflows to improve reproducibility, reduce command drift, and speed up troubleshooting handoffs.

## When to use this page
Use when configure commands are repeatedly copied/edited across machines, CI jobs, or team members, and failures are hard to reproduce.

## Why presets help
- Keep configure intent in versioned JSON instead of shell history.
- Reduce typo/flag drift between local and CI runs.
- Make escalation easier by sharing preset name + overrides.

## Minimal adoption pattern

### 1) Create baseline configure presets
Define a small set first:
- `serial-release-min`
- `mpi-release-min`
- `serial-debug-triage`

Each preset should lock:
- generator
- compilers/toolchain family
- MPI on/off intent
- install prefix convention
- package scope baseline

### 2) Keep package scope conservative initially
- Start with known-good minimal package enables.
- Add optional packages in separate derived presets.
- Avoid “all packages” as default preset.

### 3) Add build and test presets
Pair each configure preset with matching build/test presets so CI and local usage stay aligned.

### 4) Use inheritance for controlled variation
- Base preset: shared flags and policy defaults.
- Child presets: MPI toggle, debug/release, package expansions.
- Keep differences explicit and small.

## Suggested operating workflow
1. Run with preset: `cmake --preset <name>`
2. Build with paired preset: `cmake --build --preset <name>`
3. If failure occurs, capture logs and preset used (`57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md`).
4. If changing compiler/MPI/TPL roots, perform cache reset protocol before reusing preset (`47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`).

## Minimal `CMakePresets.json` skeleton (starter)
```json
{
  "version": 6,
  "cmakeMinimumRequired": { "major": 3, "minor": 23, "patch": 0 },
  "configurePresets": [
    {
      "name": "serial-release-min",
      "generator": "Ninja",
      "binaryDir": "build/serial-release-min",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release",
        "CMAKE_INSTALL_PREFIX": "${sourceDir}/install/serial-release-min",
        "TPL_ENABLE_MPI": "OFF"
      }
    },
    {
      "name": "mpi-release-min",
      "inherits": "serial-release-min",
      "binaryDir": "build/mpi-release-min",
      "cacheVariables": {
        "TPL_ENABLE_MPI": "ON"
      }
    }
  ],
  "buildPresets": [
    { "name": "serial-release-min", "configurePreset": "serial-release-min" },
    { "name": "mpi-release-min", "configurePreset": "mpi-release-min" }
  ]
}
```
Use this as a shape/template, then add Trilinos package/TPL toggles from your known-good starter profile.

## Common pitfalls and controls
- **Pitfall:** Preset points to stale compiler path.
  - **Control:** validate toolchain identity in failure capture bundle.
- **Pitfall:** Local override diverges from CI preset.
  - **Control:** prefer inherited child presets over ad-hoc `-D` flags.
- **Pitfall:** Reusing build dir across incompatible presets.
  - **Control:** isolate build directories per preset family.

## Integration with this docs pack
- Baseline and first-success setup: `56_PLATFORM_BASELINE_AND_TOOLCHAIN_MINIMUMS.md`
- Starter package scope: `55_KNOWN_GOOD_STARTER_CONFIGS.md`
- Fast diagnostics: `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md`
- Preset-specific failure routing: `59_CMAKE_PRESETS_FAILURE_PATTERNS.md`
- Ad-hoc-to-preset migration path: `60_ADHOC_TO_PRESET_MIGRATION_CHECKLIST.md`
- Cache reset discipline: `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`

## Provenance
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/sampleScripts/*`
- `Trilinos/cmake/*` (build-system conventions and options)
- Official docs index: <https://trilinos.github.io/documentation.html>
