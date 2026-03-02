# Trilinos Build and Install Command Cheatsheet

## Scope
Copy/paste command patterns for common Trilinos configure/build/install workflows.

## Audience
- Engineers who need quick command recall
- LLM agents that should return concise executable snippets first

## 1) Minimal non-MPI bootstrap
```bash
mkdir -p <build-dir> && cd <build-dir>
cmake -GNinja \
  -DCMAKE_BUILD_TYPE=Debug \
  -DCMAKE_C_COMPILER=<path-to-cc> \
  -DCMAKE_CXX_COMPILER=<path-to-cxx> \
  -DTrilinos_ENABLE_<Package>=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>

ninja install
```

## 2) MPI-focused selected-package build
```bash
mkdir -p <build-dir> && cd <build-dir>
cmake -GNinja \
  -DTPL_ENABLE_MPI=ON \
  -DMPI_BASE_DIR=<path-to-mpi> \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DTrilinos_ENABLE_<PackageA>=ON \
  -DTrilinos_ENABLE_<PackageB>=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>

ninja install
```

## 3) Broad package validation build (after TPL readiness)
```bash
mkdir -p <build-dir> && cd <build-dir>
cmake -GNinja \
  -DTPL_ENABLE_MPI=ON \
  -DMPI_BASE_DIR=<path-to-mpi> \
  -DTrilinos_ENABLE_ALL_PACKAGES=ON \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>

ninja install
```

## 4) Make-based equivalent (if Ninja unavailable)
```bash
mkdir -p <build-dir> && cd <build-dir>
cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DTrilinos_ENABLE_<Package>=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>

make -j<n> install
```

## 5) Downstream project configure against installed Trilinos
```bash
mkdir -p <downstream-build-dir> && cd <downstream-build-dir>
cmake \
  -DTrilinos_DIR=<install-prefix>/lib/cmake/Trilinos \
  <path-to-downstream-source>

cmake --build . -j<n>
```

## Quick flags to remember
- Enable tests: `-DTrilinos_ENABLE_TESTS=ON`
- Shared libs: `-DBUILD_SHARED_LIBS=ON`
- Disable Fortran: `-DTrilinos_ENABLE_Fortran=OFF`
- Enable one package: `-DTrilinos_ENABLE_<Package>=ON`

## Cross references
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `13_CMAKE_FLAG_QUICK_REFERENCE.md`
- `26_BUILD_INSTALL_DECISION_TREE.md`
- `29_INSTALL_VERIFICATION_CHECKLIST.md`
- `30_CMAKE_CONFIGURE_SCRIPT_TEMPLATE_LIBRARY.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- Companion command guidance in local `library/Trilinos/` docs
