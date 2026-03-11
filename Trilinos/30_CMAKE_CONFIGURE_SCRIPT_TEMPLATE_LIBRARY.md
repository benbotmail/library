# Trilinos CMake Configure Script Template Library

## Scope
Reusable configure-script templates for common Trilinos build scenarios, designed to reduce option drift and improve reproducibility.

## Why this page exists
Repeated ad-hoc CMake command editing is a frequent source of build churn. Stable `do-configure` templates make local and CI builds easier to reproduce and debug.

## Template usage pattern
- Copy the template matching your scenario.
- Fill placeholders (`<...>`).
- Keep one script per profile/build directory.
- Commit or archive scripts with build notes for team reuse.

## Template A — Minimal non-MPI debug bootstrap
```bash
#!/usr/bin/env bash
set -euo pipefail

cmake \
  -GNinja \
  -DCMAKE_BUILD_TYPE=Debug \
  -DCMAKE_C_COMPILER=<path-to-cc> \
  -DCMAKE_CXX_COMPILER=<path-to-cxx> \
  -DTrilinos_ENABLE_<CorePackage>=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>
```

## Template B — MPI-focused package set
```bash
#!/usr/bin/env bash
set -euo pipefail

cmake \
  -GNinja \
  -DTPL_ENABLE_MPI=ON \
  -DMPI_BASE_DIR=<path-to-mpi> \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DTrilinos_ENABLE_<PackageA>=ON \
  -DTrilinos_ENABLE_<PackageB>=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>
```

## Template C — Broad validation profile (use after TPL readiness)
```bash
#!/usr/bin/env bash
set -euo pipefail

cmake \
  -GNinja \
  -DTPL_ENABLE_MPI=ON \
  -DMPI_BASE_DIR=<path-to-mpi> \
  -DTrilinos_ENABLE_ALL_PACKAGES=ON \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>
```

## Template D — Preset-based wrapper (local + CI parity)
```bash
#!/usr/bin/env bash
set -euo pipefail

cmake --preset <configure-preset>
cmake --build --preset <build-preset>
```

Optional install step:
```bash
cmake --install <build-dir>
```

## Template hygiene checklist
- Keep compiler and MPI choices explicit.
- Avoid switching major options within the same build tree.
- Pair each template with a named build directory (`build-mpi-relwithdebinfo`, etc.).
- Prefer preset inheritance over ad-hoc per-run `-D` overrides.
- Record package rationale near template comments.

## Cross references
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `13_CMAKE_FLAG_QUICK_REFERENCE.md`
- `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`
- `26_BUILD_INSTALL_DECISION_TREE.md`
- `29_INSTALL_VERIFICATION_CHECKLIST.md`
- `58_CMAKE_PRESETS_ADOPTION_GUIDE.md`
- `59_CMAKE_PRESETS_FAILURE_PATTERNS.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- Configure/build conventions reflected in local Trilinos docs pack guidance
