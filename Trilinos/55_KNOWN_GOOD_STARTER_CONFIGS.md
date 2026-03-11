# Known-Good Starter Configs (Build/Install)

## Scope
Quick-start configuration baselines with minimal assumptions, intended to reduce first-attempt configure failures.

## Audience
- Engineers who want a reliable first build before tuning options
- LLM agents that need deterministic starter recommendations

## When to use this page
Use these presets when you need a conservative first pass. After a successful build/install, expand package scope and performance flags incrementally.

## Starter A: Minimal serial baseline (no MPI)
Use this when you want the lowest-friction initial success path.

```bash
cmake \
  -GNinja \
  -DCMAKE_BUILD_TYPE=Release \
  -DTrilinos_ENABLE_TESTS=OFF \
  -DTrilinos_ENABLE_EXAMPLES=OFF \
  -DTrilinos_ENABLE_Fortran=OFF \
  -DTrilinos_ENABLE_ALL_OPTIONAL_PACKAGES=OFF \
  -DTrilinos_ENABLE_<PrimaryPackage>=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>

ninja -j<n>
ninja install
```

### Why this works
- Keeps dependency surface small
- Disables optional fan-out that often triggers missing TPL failures
- Produces a valid install usable for downstream smoke checks

## Starter B: Conservative MPI baseline
Use this when MPI is required, while keeping package/TPL pressure controlled.

```bash
cmake \
  -GNinja \
  -DCMAKE_BUILD_TYPE=Release \
  -DTPL_ENABLE_MPI=ON \
  -DMPI_BASE_DIR=<path-to-mpi> \
  -DTrilinos_ENABLE_TESTS=OFF \
  -DTrilinos_ENABLE_EXAMPLES=OFF \
  -DTrilinos_ENABLE_ALL_OPTIONAL_PACKAGES=OFF \
  -DTrilinos_ENABLE_<PrimaryPackage>=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>

ninja -j<n>
ninja install
```

### Why this works
- Preserves MPI requirement while avoiding broad package explosion
- Reduces early failures from optional TPL discovery

## Expansion order after first success
1. Add one required Trilinos package at a time.
2. Reconfigure and rebuild after each addition.
3. Only then enable tests/examples for target packages.
4. Enable optional packages last, with explicit TPL path hints as needed.

## Guardrails
- Do **not** start with `Trilinos_ENABLE_ALL_PACKAGES=ON` unless your TPL/toolchain stack is already known-good.
- Keep compiler family and C++ standard consistent between Trilinos and downstream apps.
- If configure cache becomes noisy or contradictory, reset cache before reconfigure.

## Related pages
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`
- `31_TPL_DISCOVERY_AND_PATH_HINTS.md`
- `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- `Trilinos/cmake/tribits/core/package_arch/TribitsGlobalMacros.cmake`
- `Trilinos/PackagesList.cmake`
- Official docs index: <https://trilinos.github.io/documentation.html>
