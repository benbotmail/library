# Trilinos Build & Install Playbook

## Scope
Quick, reliable paths to configure, build, and install Trilinos.

## Audience
- Engineers needing a practical refresh
- LLM agents generating build guidance from source-aligned docs

## Prerequisites
- CMake >= 3.23.0
- C and C++ compilers
- Optional: Fortran compiler
- Optional: MPI install

## Content

## Fast path A: MPI build (broad package enable)
```bash
cmake \
  -DTPL_ENABLE_MPI=ON \
  -DMPI_BASE_DIR=<path-to-mpi> \
  -DTrilinos_ENABLE_ALL_PACKAGES=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>

make -j<n> install
```

### Notes
- Enabling all packages may trigger required third-party libraries (TPLs).
- If configure fails on missing TPLs, either point to TPL locations or disable affected packages.

## Fast path B: non-MPI build
```bash
cmake \
  -DCMAKE_C_COMPILER=<path-to-cc> \
  -DCMAKE_CXX_COMPILER=<path-to-cxx> \
  -DCMAKE_Fortran_COMPILER=<path-to-fc> \
  -DTrilinos_ENABLE_ALL_PACKAGES=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>

make -j<n> install
```

## Focused package build (MPI + selected packages)
```bash
cmake \
  -DTPL_ENABLE_MPI=ON \
  -DMPI_BASE_DIR=<path-to-mpi> \
  -DTrilinos_ENABLE_Epetra=ON \
  -DTrilinos_ENABLE_AztecOO=ON \
  -DTrilinos_ENABLE_Ifpack=ON \
  -DCMAKE_INSTALL_PREFIX=<install-prefix> \
  <path-to-trilinos-source>

make -j<n> install
```

## Useful options
- Ninja generator: `-GNinja`
- Shared libs: `-DBUILD_SHARED_LIBS=ON`
- Enable float support: `-DTrilinos_ENABLE_FLOAT=ON`
- Enable complex scalars: `-DTrilinos_ENABLE_COMPLEX=ON`
- Disable Fortran: `-DTrilinos_ENABLE_Fortran=OFF`
- Enable tests: `-DTrilinos_ENABLE_TESTS=ON`
- Enable one package: `-DTrilinos_ENABLE_<PackageName>=ON`

## Downstream build against installed Trilinos
See: `demos/simpleBuildAgainstTrilinos/README.md` in repo.

## Troubleshooting quick triage
1. **Missing TPL at configure time**
   - Cause: `Trilinos_ENABLE_ALL_PACKAGES=ON` pulled package requirements not present locally.
   - Fix: set required TPL paths or disable those packages.
2. **Compiler wrapper mismatch in MPI builds**
   - Cause: wrong compiler pair (system compiler vs MPI wrappers).
   - Fix: use MPI wrapper compilers consistently for C/C++/Fortran.
3. **Unclear package set**
   - Use configure output or package list docs (`PackagesList.cmake`) to choose targeted enables.

## Validation
- Confirm CMake version is at least 3.23.0 before configure.
- For MPI builds, verify MPI wrapper/compiler consistency (`mpicc`, `mpicxx`, optional `mpifort`).
- After install, validate downstream consumption using `demos/simpleBuildAgainstTrilinos`.

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- `Trilinos/PackagesList.cmake`
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- Official docs index: <https://trilinos.github.io/documentation.html>
