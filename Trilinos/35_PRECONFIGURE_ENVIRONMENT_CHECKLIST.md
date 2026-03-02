# Trilinos Pre-Configure Environment Checklist

## Scope
Preflight checks to run before Trilinos CMake configure, aimed at preventing avoidable configure/build failures.

## Use case
Run this checklist on a new machine, fresh container, or after major toolchain updates.

## Preflight checklist

1. **CMake version is supported**
   - Confirm CMake is at least the minimum required by Trilinos docs (`>= 3.23.0`).

2. **Compiler toolchain is intentional**
   - Confirm chosen C and C++ compilers are installed and callable.
   - If Fortran is needed, confirm Fortran compiler availability.

3. **MPI decision is explicit**
   - Decide up front whether this profile is MPI or non-MPI.
   - For MPI profile, confirm wrapper/compiler commands are available and consistent.

4. **Build/install directories are prepared**
   - Use an out-of-source build directory.
   - Create/install prefix path with expected permissions.

5. **Package scope is defined**
   - Prefer minimal package set for first success.
   - Avoid broad package enables until baseline configure succeeds.

6. **TPL expectations are known**
   - If selected packages require TPLs, confirm install roots are known.
   - Plan to pass explicit hints/paths in configure script.

7. **Configure script is captured**
   - Store configure invocation in a reusable script (`do-configure`) for reproducibility.

8. **Parallel build plan is set**
   - Choose build generator (`Ninja` or Make) and safe parallelism level.

9. **Post-install validation step is planned**
   - Include downstream smoke build in acceptance criteria before declaring success.

## Quick go/no-go
Proceed to configure only when:
- Toolchain choice is clear,
- package scope is bounded,
- build/install paths are ready,
- and required dependency hints are identified.

## Cross references
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `26_BUILD_INSTALL_DECISION_TREE.md`
- `29_INSTALL_VERIFICATION_CHECKLIST.md`
- `30_CMAKE_CONFIGURE_SCRIPT_TEMPLATE_LIBRARY.md`
- `31_TPL_DISCOVERY_AND_PATH_HINTS.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- Build hygiene conventions synthesized from local `library/Trilinos/` build/install docs
