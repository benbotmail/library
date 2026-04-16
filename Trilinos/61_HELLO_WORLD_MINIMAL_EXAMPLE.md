# Hello World Minimal Example

## Scope
Complete minimal working example demonstrating Trilinos installation and downstream usage with a concrete C++ application.

## Audience
- New users needing a first success milestone
- Engineers validating their Trilinos installation
- LLM systems generating working code examples

## Prerequisites
- CMake >= 3.23
- C++ compiler supporting C++17
- Trilinos source tree accessible
- Familiarity with basic C++ and CMake

## Content

### Example Overview
This example builds a minimal Trilinos installation and uses it in a downstream C++ application. The example uses Teuchos (core utility library) to keep dependencies minimal.

### Step 1: Install Trilinos (Minimal Serial)

Create a clean workspace and install Trilinos with minimal package scope:

```bash
# Setup directories
mkdir -p ~/trilinos-build ~/trilinos-install
cd ~/trilinos-build

# Configure minimal Trilinos build (Teuchos only)
cmake \
  -GNinja \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=~/trilinos-install \
  -DTrilinos_ENABLE_TESTS=OFF \
  -DTrilinos_ENABLE_EXAMPLES=OFF \
  -DTrilinos_ENABLE_Fortran=OFF \
  -DTrilinos_ENABLE_ALL_OPTIONAL_PACKAGES=OFF \
  -DTrilinos_ENABLE_Teuchos=ON \
  /path/to/trilinos/source

# Build and install
ninja -j$(nproc)
ninja install
```

**Build time:** ~5-15 minutes on a modern multi-core machine
**Disk space:** ~300-500 MB

### Step 2: Create Downstream Application

Create a new directory for your application:

```bash
mkdir -p ~/hello-trilinos
cd ~/hello-trilinos
```

#### C++ Source File (`main.cpp`)

```cpp
#include <Teuchos_Version.hpp>
#include <Teuchos_CommandLineProcessor.hpp>
#include <iostream>
#include <string>

int main(int argc, char** argv) {
    std::cout << "Hello from Trilinos!" << std::endl;
    std::cout << "Teuchos version: " << Teuchos::version() << std::endl;

    // Basic Teuchos command line processing example
    Teuchos::CommandLineProcessor clp;
    clp.setDocString("Simple Trilinos hello world example\n");

    int verbosity = 1;
    clp.setOption("verbose", &verbosity, "Verbosity level (0-3)");

    Teuchos::CommandLineProcessor::EParseCommandLineReturn parse_return =
        clp.parse(argc, argv);

    if (parse_return == Teuchos::CommandLineProcessor::PARSE_HELP_PRINTED) {
        return 0;
    }

    std::cout << "Verbosity level: " << verbosity << std::endl;

    return 0;
}
```

#### CMake Build File (`CMakeLists.txt`)

```cmake
cmake_minimum_required(VERSION 3.23)
project(HelloTrilinos CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Find Trilinos
find_package(Trilinos REQUIRED
  CONFIG
  HINTS ~/trilinos-install/lib/cmake/Trilinos
  NO_DEFAULT_PATH
)

# Define includes and libraries
include_directories(${Trilinos_INCLUDE_DIRS})
include_directories(${Trilinos_TPL_INCLUDE_DIRS})

# Build executable
add_executable(hello_trilinos main.cpp)

# Link against Teuchos
target_link_libraries(hello_trilinos
  ${Trilinos_LIBRARIES}
  ${Trilinos_TPL_LIBRARIES}
)

# Optional: Print found packages for verification
message(STATUS "Found Trilinos version: ${Trilinos_VERSION}")
message(STATUS "Trilinos packages: ${Trilinos_PACKAGES}")
message(STATUS "Trilinos libraries: ${Trilinos_LIBRARIES}")
```

### Step 3: Build and Run

```bash
# Create build directory
mkdir build
cd build

# Configure
cmake \
  -DCMAKE_PREFIX_PATH=~/trilinos-install \
  ..

# Build
cmake --build . -j$(nproc)

# Run the example
./hello_trilinos

# Run with command-line options
./hello_trilinos --verbose=2
./hello_trilinos --help
```

### Expected Output

```
Hello from Trilinos!
Teuchos version: 13.3.1
Verbosity level: 1
```

### What This Example Demonstrates

1. **Minimal Trilinos installation**: Installing only the Teuchos package keeps build time short (~5-15 min)
2. **Downstream CMake integration**: Using `find_package(Trilinos)` to locate installation
3. **Core Teuchos usage**: Version checking and command-line processing
4. **Working end-to-end flow**: From Trilinos source to working downstream application

### Common Issues and Fixes

| Issue | Symptom | Fix |
|-------|---------|-----|
| `Could not find Trilinos` | CMake configure fails | Check `CMAKE_PREFIX_PATH` points to install prefix |
| Undefined symbols during link | Linker errors | Verify `${Trilinos_LIBRARIES}` includes all required libraries |
| Version mismatch at runtime | Crashes or unexpected behavior | Ensure compiler family and C++ standard match Trilinos build |
| Multiple Trilinos installations | Wrong libraries found | Use `NO_DEFAULT_PATH` in `find_package` and explicit `HINTS` |

### Next Steps After Success

Once this minimal example works, you can:

1. **Add more packages**: Enable Kokkos, Tpetra, or other packages incrementally
2. **Add MPI support**: Use `Starter B` from `55_KNOWN_GOOD_STARTER_CONFIGS.md`
3. **Explore package-specific examples**: Each Trilinos package has its own examples directory
4. **Use more Teuchos features**: Parameter lists, array views, smart pointers

### Verification Checklist

- [ ] Trilinos installation completes without errors
- [ ] Install directory contains `lib/cmake/Trilinos/TrilinosConfig.cmake`
- [ ] CMake configure finds Trilinos successfully
- [ ] Application builds without linker errors
- [ ] Application runs and prints expected output
- [ ] Command-line options work correctly

## Related Documentation

- `04_BUILD_INSTALL_PLAYBOOK.md` — Detailed install procedures
- `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md` — Downstream integration patterns
- `32_30_MINUTE_FIRST_SUCCESS_PATH.md` — Time-boxed first success workflow
- `55_KNOWN_GOOD_STARTER_CONFIGS.md` — Conservative starter configurations

## Validation

- Test this example on a clean environment to confirm it works end-to-end
- Verify build time estimates are accurate for typical workstations
- Ensure all paths and options are valid for current Trilinos releases

## Provenance

- `Trilinos/packages/teuchos/example/CommandLineProcessing/` — Teuchos command-line processing examples
- `Trilinos/INSTALL.rst` — Installation procedures
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md` — Downstream integration patterns
- Teuchos documentation: <https://trilinos.github.io/teuchos.html>
