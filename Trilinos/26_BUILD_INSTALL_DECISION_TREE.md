# Trilinos Build/Install Decision Tree

## Scope
Fast decision tree for selecting a Trilinos configure/build/install path with minimal rework.

## When to use this page
Use this when you need to quickly choose **which build strategy to start with** (MPI vs non-MPI, minimal vs broad package set, debug vs release), before consulting deeper references.

## Decision tree

1. **Do you need MPI in downstream runs?**
   - **Yes** → Start with `-DTPL_ENABLE_MPI=ON` and consistent MPI wrappers.
   - **No / uncertain** → Start non-MPI for faster first success and add MPI later if needed.

2. **Is this a first compile on a new machine?**
   - **Yes** → Prefer a **minimal package profile** first; verify toolchain and install path.
   - **No** → Reuse a known-good profile and adjust one variable at a time.

3. **Do you need many packages immediately?**
   - **No** → Enable only required packages (`-DTrilinos_ENABLE_<Pkg>=ON`).
   - **Yes** → Broader enable can work, but expect more TPL obligations and longer configure cycles.

4. **Do you need fast diagnostics or performance first?**
   - **Diagnostics first** → Use `Debug` profile and optionally enable tests.
   - **Performance first** → Use `Release` (or `RelWithDebInfo` for mixed needs).

5. **Do you already know required TPL locations?**
   - **Yes** → Provide explicit TPL roots/flags at configure time.
   - **No** → Start with package subset that avoids unavailable TPLs, then expand.

6. **Will downstream projects consume installed Trilinos?**
   - **Yes** → Set stable `CMAKE_INSTALL_PREFIX`, install, then validate against `demos/simpleBuildAgainstTrilinos`.
   - **No** → A local build-only flow may be sufficient for immediate experiments.

## Recommended starting profiles
- **Safe first success:** non-MPI + minimal package set + Debug
- **Common HPC baseline:** MPI + selected package set + RelWithDebInfo
- **Broad validation pass:** MPI + wider package coverage after TPL readiness confirmed

## Escalation triggers (switch strategy)
- Configure fails on missing TPLs repeatedly → reduce package scope and add TPLs incrementally.
- MPI link/compiler mismatch errors → standardize on MPI wrapper compilers.
- Build time too high for iteration needs → reduce enabled packages and disable tests temporarily.

## Cross references
- Build execution details: `04_BUILD_INSTALL_PLAYBOOK.md`
- CMake toggles and flags: `13_CMAKE_FLAG_QUICK_REFERENCE.md`
- Profile templates: `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`
- Package-selection strategy: `17_PACKAGE_SELECTION_STRATEGY.md`
- Stage-based failure routing: `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- `Trilinos/PackagesList.cmake`
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- Local companion docs in `library/Trilinos/` listed in Cross references
