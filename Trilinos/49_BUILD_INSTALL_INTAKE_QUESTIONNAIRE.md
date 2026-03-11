# Build/Install Intake Questionnaire

## Scope
Provide a compact intake questionnaire to gather the minimum high-value context before giving Trilinos build/install troubleshooting guidance.

## Audience
- Engineers requesting Trilinos build/install help
- LLM assistants collecting context before diagnosis

## Prerequisites
- Access to user’s failing command/output (or ability to rerun)

## Content

### Ask these first (in order)
1. **Failure stage**
   - Is the failure in `configure`, `build`, `install`, or `runtime`?
2. **Exact decisive error**
   - Paste the first clear error line, not only the final summary line.
3. **Environment fingerprint**
   - OS/kernel, CMake version, compiler versions, MPI implementation/wrappers (if used).
4. **Configure invocation**
   - Exact configure command or script.
5. **Build profile/scope**
   - Minimal vs broad package set; shared/static choice; build type.
6. **Dependency/TPL context**
   - Any explicit TPL paths or known missing dependencies.
7. **Cache freshness**
   - Was this attempted from a clean build directory after recent changes?
8. **Preset context (if using presets)**
   - Preset name(s) used (`configure`/`build`), and whether `CMakeUserPresets.json` or CI-only overrides are involved.
9. **Repro status**
   - Can issue be reproduced with a reduced/minimal profile?

### One-message intake template
```text
Stage:
First decisive error:
OS/kernel:
CMake:
Compiler C/C++:
MPI wrappers (if used):
Configure command/script:
Profile/package scope:
TPL/path hints used:
Preset(s) used (configure/build):
Any CMakeUserPresets.json or CI-only overrides? (yes/no + brief note)
Clean build dir used after recent changes? (yes/no)
Minimal reproducer available? (yes/no)
```

### Routing guidance after intake
- Missing/undetected TPLs → `31_TPL_DISCOVERY_AND_PATH_HINTS.md`
- Configure logic failures → `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- Install/runtime loader/ABI issues → `39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`
- Need strict sequencing → `48_TRIAGE_DECISION_ORDER_BUILD_INSTALL_ISSUES.md`
- Need escalation packet → `45_BUILD_INSTALL_ESCALATION_HANDOFF_CHECKLIST.md`
- Need immediate post-failure diagnostics capture → `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md`
- Need pre-configure baseline reset before retry → `56_PLATFORM_BASELINE_AND_TOOLCHAIN_MINIMUMS.md`
- Need preset-specific failure routing (local/CI divergence) → `59_CMAKE_PRESETS_FAILURE_PATTERNS.md`

## Validation
- Questionnaire captures stage, environment, command, and reproducibility context.
- Template is short enough for first-turn troubleshooting intake.

## Provenance
- `library/Trilinos/42_BUILD_INSTALL_MINIMUM_CONTEXT_SCHEMA.md`
- `library/Trilinos/43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md`
- `library/Trilinos/48_TRIAGE_DECISION_ORDER_BUILD_INSTALL_ISSUES.md`
- `library/Trilinos/45_BUILD_INSTALL_ESCALATION_HANDOFF_CHECKLIST.md`
