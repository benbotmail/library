# CMake Cache Reset and Reconfigure Protocol

## Scope
Define a standard reset-and-reconfigure procedure for Trilinos builds when configure state is suspected stale or inconsistent after toolchain, MPI, TPL, or major flag changes.

## Audience
- Engineers debugging persistent configure/build anomalies
- LLM assistants recommending safe recovery steps after environment changes

## Prerequisites
- Trilinos source directory
- Existing (possibly stale) build directory
- Ability to rerun configure with explicit options

## Content

### When to apply this protocol
Use this protocol when any of the following changed since last configure:
- compiler family/version or compiler path
- MPI wrapper/implementation
- major TPL path hints
- shared/static library strategy
- broad package enable/disable profile

### Why stale cache causes false failures
CMake caches detection results and path decisions. Reusing an old cache after significant environment changes can produce misleading failures that are not reproducible from a clean state.

### Reset protocol (safe default)
1. Preserve current configure invocation (script or command line).
2. Archive or remove the old build directory.
3. Create a fresh build directory.
4. Re-run configure with explicit compiler/MPI/TPL options.
5. Validate cache values before starting full build.

### Minimal command pattern
```bash
# from Trilinos source root
mv build build.stale.$(date +%Y%m%d-%H%M%S) 2>/dev/null || true
mkdir -p build
cd build
# re-run your explicit configure command/script here
```

### Post-reset sanity checks
- Confirm compiler and wrapper paths in `CMakeCache.txt` match intent.
- Confirm critical toggles (package set, shared/static, install prefix).
- Run a minimal build target first before broad parallel build.

### Escalation trigger
If failures persist after a clean reset and minimal configuration, move to minimal-repro and escalation-handoff workflows.

## Validation
- Procedure isolates stale cache effects from genuine toolchain/package issues.
- Steps are compatible with existing configure/build and escalation docs.

## Provenance
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- `library/Trilinos/43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md`
- `library/Trilinos/45_BUILD_INSTALL_ESCALATION_HANDOFF_CHECKLIST.md`
- Trilinos upstream repository docs: <https://github.com/trilinos/Trilinos>
- Official Trilinos documentation index: <https://trilinos.github.io/documentation.html>
