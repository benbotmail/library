# Trilinos 30-Minute First-Success Path

## Scope
A condensed, time-boxed path to achieve a first successful Trilinos configure/build/install cycle with minimal scope.

## Target outcome
Within ~30 minutes, produce a usable Trilinos install prefix validated by a downstream smoke build.

## Assumptions
- CMake >= 3.23.0 available
- Working C/C++ toolchain
- Local Trilinos source checkout is present

## Time-box plan

### 0–5 min: Prepare clean workspace
1. Create out-of-source build and install directories.
2. Decide on a **minimal non-MPI** initial profile unless MPI is immediately required.
3. Record intended configure options in a `do-configure` script.

### 5–12 min: Configure minimal package scope
1. Enable only 1–3 essential packages for your immediate use case.
2. Set explicit compilers and install prefix.
3. Run CMake configure.

If configure fails:
- Reduce package scope further.
- Resolve one missing dependency class at a time.

### 12–24 min: Build + install
1. Build with parallel workers (`ninja` or `make -j<n>`).
2. Install into chosen prefix.

If build fails:
- Route by stage and error pattern (configure vs compile vs link).
- Avoid broad option changes mid-cycle.

### 24–30 min: Verify downstream readiness
1. Confirm Trilinos CMake config files exist in install tree.
2. Run a downstream smoke configure/build against install prefix.
3. Save final configure script for reproducibility.

## Success criteria
- Configure completed for selected package set.
- Install completed into a stable prefix.
- Downstream smoke project successfully finds and links Trilinos.

## Escalation after first success
- Add MPI profile if needed.
- Expand package set incrementally.
- Introduce broader test coverage and CI lanes.

## Cross references
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`
- `26_BUILD_INSTALL_DECISION_TREE.md`
- `29_INSTALL_VERIFICATION_CHECKLIST.md`
- `30_CMAKE_CONFIGURE_SCRIPT_TEMPLATE_LIBRARY.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- Companion procedures in local `library/Trilinos/` docs
