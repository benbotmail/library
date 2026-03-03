# Trilinos Configure Failure Triage Playbook

## Scope
Step-by-step triage flow for failures that occur during the **CMake configure** stage (before compile/build).

## Why this matters
Most costly Trilinos setup loops begin at configure time: missing dependencies, inconsistent toolchains, or over-broad package selection. Fast triage here saves the most total time.

## Triage flow

1. **Classify the failure first**
   - Confirm this is configure-stage (CMake generation did not complete).
   - Do not apply compile/link fixes yet.

2. **Capture exact failing message block**
   - Preserve the first decisive error message from configure output.
   - Avoid debugging from secondary cascaded warnings.

3. **Check scope pressure**
   - If broad package enable was used, reduce to minimal/targeted packages.
   - Re-run configure with reduced scope to isolate root cause.

4. **Check toolchain consistency**
   - Verify intended compiler family/version is explicit.
   - For MPI builds, ensure wrapper usage is consistent across languages.

5. **Check TPL discoverability**
   - Provide explicit roots/hints for required dependencies.
   - Reconfigure from a clean out-of-source build directory.

6. **Re-run with one change at a time**
   - Apply one fix, rerun, observe, repeat.
   - Avoid multi-variable edits that obscure cause/effect.

## Fast symptom router

### “Required TPL not found”
- Reduce package set and add explicit TPL hints incrementally.

### “Compiler test failed” / language not enabled as expected
- Confirm compiler executables and flags; ensure profile matches intended language requirements.

### MPI detection/config inconsistency
- Standardize on MPI wrapper toolchain for MPI profile and rebuild cleanly.

### Configure takes too long and fails late
- Start from minimal package profile for faster failure localization.

## Recovery checklist before retry
- Clean build directory (or new one)
- Stable configure script (`do-configure`) updated
- Package scope intentionally bounded
- Dependency roots explicit
- Toolchain mode (MPI/non-MPI) explicit

## Cross references
- `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`
- `26_BUILD_INSTALL_DECISION_TREE.md`
- `31_TPL_DISCOVERY_AND_PATH_HINTS.md`
- `35_PRECONFIGURE_ENVIRONMENT_CHECKLIST.md`
- `04_BUILD_INSTALL_PLAYBOOK.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- Configure-stage troubleshooting patterns synthesized from local Trilinos docs pack guidance
