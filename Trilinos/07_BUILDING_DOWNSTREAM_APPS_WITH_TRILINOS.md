# Building Downstream Applications Against Installed Trilinos

## Scope
How to configure and build a separate CMake application against an installed Trilinos build.

## When to use this
Use this workflow when Trilinos is already installed and your application should consume it via `find_package(...)`.

## Prerequisites
- Installed Trilinos containing required packages (at minimum for the demo: `Tpetra`, with `Teuchos` used in the CMake example).
- Compatible compiler toolchain between Trilinos build and downstream app build.
- MPI available and working for MPI-based examples/tests.

## Step 1: Build/install Trilinos with required packages
Example baseline:
```bash
cmake \
  -D CMAKE_INSTALL_PREFIX=<tril-install-prefix> \
  -D TPL_ENABLE_MPI=ON \
  -D Trilinos_ENABLE_Tpetra=ON \
  -D CMAKE_C_COMPILER=<c-compiler> \
  -D CMAKE_CXX_COMPILER=<cxx-compiler> \
  <trilinos-src>

make -j4 install
```

## Step 2: Configure downstream application with `CMAKE_PREFIX_PATH`
```bash
cmake \
  -D CMAKE_PREFIX_PATH=<tril-install-prefix> \
  -D CMAKE_C_COMPILER=<c-compiler> \
  -D CMAKE_CXX_COMPILER=<cxx-compiler> \
  <path-to-your-app-source>
```

## Step 3: Build and test application
```bash
make -j4
ctest
```

## Recommended CMake pattern in downstream app
```cmake
find_package(Trilinos REQUIRED COMPONENTS Teuchos Tpetra)
add_executable(MyApp app.cpp)
target_link_libraries(MyApp Trilinos::all_selected_libs)
```

## CUDA/toolchain compatibility note
For CUDA-oriented builds, compiler compatibility must match Trilinos configuration. The upstream demo notes using `nvcc_wrapper` with MPI setups (for example setting `OMPI_CXX` to `nvcc_wrapper` when appropriate).

## Frequent failure modes
1. **`find_package(Trilinos)` fails**
   - Check `CMAKE_PREFIX_PATH` points to Trilinos install prefix containing `TrilinosConfig.cmake`.
2. **Compiler mismatch errors**
   - Use compatible compilers and wrappers consistent with the Trilinos build.
3. **MPI test runtime fails**
   - Verify MPI runtime and launcher configuration (`mpiexec`) on host.

## Validation hook available in Trilinos
The demo path is validated via `TrilinosInstallTests` when Trilinos is configured with:
- `TPL_ENABLE_MPI=ON`
- `Trilinos_ENABLE_Tpetra=ON`
- `Trilinos_ENABLE_TrilinosInstallTests=ON`

and tests are run with:
```bash
ctest -j4 -R TrilinosInstallTests
```

## Provenance
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- `Trilinos/demos/simpleBuildAgainstTrilinos/CMakeLists.txt`
- `Trilinos/INSTALL.rst`
