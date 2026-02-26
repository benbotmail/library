# Building Downstream Applications Against Installed Trilinos

## Scope
How to configure and build a separate CMake application against an installed Trilinos build.

## Audience
- Engineers integrating their own CMake projects with Trilinos
- LLM systems generating downstream integration instructions

## Prerequisites
- Installed Trilinos containing required packages (minimum for demo pattern: `Tpetra`; `Teuchos` used in the sample CMake).
- Compatible compiler toolchain between Trilinos build and downstream app build.
- MPI available and working for MPI-based examples/tests.

## Content

### When to use this workflow
Use this path when Trilinos is already installed and the application should consume it via `find_package(...)`.

### Step 1: Build/install Trilinos with required packages
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

### Step 2: Configure downstream application with `CMAKE_PREFIX_PATH`
```bash
cmake \
  -D CMAKE_PREFIX_PATH=<tril-install-prefix> \
  -D CMAKE_C_COMPILER=<c-compiler> \
  -D CMAKE_CXX_COMPILER=<cxx-compiler> \
  <path-to-your-app-source>
```

### Step 3: Build and test application
```bash
make -j4
ctest
```

### Recommended CMake pattern in downstream app
```cmake
find_package(Trilinos REQUIRED COMPONENTS Teuchos Tpetra)
add_executable(MyApp app.cpp)
target_link_libraries(MyApp Trilinos::all_selected_libs)
```

### CUDA and toolchain compatibility note
For CUDA-oriented builds, compiler compatibility must match Trilinos configuration. Upstream demo guidance notes using `nvcc_wrapper` with MPI setups where appropriate (for example via `OMPI_CXX`).

### Frequent failure modes
1. **`find_package(Trilinos)` fails**
   - Check `CMAKE_PREFIX_PATH` points to install prefix containing `TrilinosConfig.cmake`.
2. **Compiler mismatch errors**
   - Use compatible compilers/wrappers consistent with the Trilinos build.
3. **MPI test runtime fails**
   - Verify MPI runtime and launcher configuration (`mpiexec`) on host.

### Trilinos validation hook
The downstream integration path is exercised via `TrilinosInstallTests` when Trilinos is configured with:
- `TPL_ENABLE_MPI=ON`
- `Trilinos_ENABLE_Tpetra=ON`
- `Trilinos_ENABLE_TrilinosInstallTests=ON`

and tested with:
```bash
ctest -j4 -R TrilinosInstallTests
```

## Validation
- Confirm installed prefix contains `TrilinosConfig.cmake`.
- Confirm downstream configure resolves `find_package(Trilinos ...)` without manual path hacks beyond `CMAKE_PREFIX_PATH`.
- Confirm at least one downstream test runs under the intended MPI/runtime setup.

## Provenance
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- `Trilinos/demos/simpleBuildAgainstTrilinos/CMakeLists.txt`
- `Trilinos/INSTALL.rst`
