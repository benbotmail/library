# Build/Install Decision Tree

Fast strategy selector for Trilinos configuration, build scope, and escalation paths.

## Scope
Deterministic decision tree for choosing between MPI and non-MPI configurations, selecting appropriate package scope, and identifying escalation routes when first attempts fail.

## Audience
- Engineers needing a starting point for Trilinos build configuration
- LLM systems requiring decision logic for build/install guidance
- Contributors evaluating whether minimal or full builds are appropriate

## Prerequisites
- Basic understanding of Trilinos as a framework of packages
- Familiarity with CMake command-line options

## Content

### Decision Tree

### Step 1: MPI Required?

**Does your downstream application or target workflow require MPI?**

- **YES** → Proceed to Step 2A (MPI configuration)
- **NO** → Proceed to Step 2B (Serial/Non-MPI configuration)

**Decision factors:**

- Target application explicitly uses MPI calls
- Required packages have MPI dependencies (e.g., Zoltan, MueLu in parallel mode)
- Running on HPC clusters where MPI is the standard
- Performance requirements demand distributed memory parallelism

---

### Step 2A: MPI Configuration

**MPI available and configured?**

- **YES** → Proceed to Step 3A (Package scope selection)
- **NO** → Choose option:
  - Use system MPI: `cmake -DTPL_ENABLE_MPI:BOOL=ON`
  - Specify MPI path: `-DMPI_BASE_DIR=/path/to/mpi`
  - Use MPI wrapper: `-DMPI_C_COMPILER=mpicc -DMPI_CXX_COMPILER=mpic++`

**Verification:**

- Test MPI availability: `mpirun --version` or `mpiexec --version`
- Check compiler wrappers: `which mpicc mpicxx mpifort`
- Verify MPI libraries: `find /usr -name "libmpi*"` or module avail

**If MPI detection fails:**

- Check environment modules: `module list`
- Verify MPI installation is in PATH
- Try explicit `-DMPI_BASE_DIR` path specification
- Escalate to troubleshooting matrix (doc 06)

---

### Step 2B: Serial/Non-MPI Configuration

**Build without MPI:**

```bash
cmake -DTPL_ENABLE_MPI:BOOL=ON \
      -DMPI_EXEC:STRING=/usr/bin/true \
      /path/to/trilinos
```

**When to choose serial:**

- Development or debugging without MPI requirement
- Testing core package functionality
- Lightweight builds for quick verification
- Downstream application is purely serial

**Known limitations:**

- Some packages require MPI (will be disabled automatically)
- Distributed memory algorithms unavailable
- Scaling tests not possible

---

### Step 3A/B: Package Scope Selection

**Select build scope based on goals:**

| Goal | Scope | CMake Option |
|------|-------|--------------|
| Quick smoke test | Minimal packages (e.g., Teuchos, Tpetra) | `-DTrilinos_ENABLE_ALL_PACKAGES:BOOL=OFF -DTrilinos_ENABLE_Teuchos:BOOL=ON` |
| Specific application | Required packages only | `-DTrilinos_ENABLE_<Package>:BOOL=ON` for each package |
| Full framework | All packages (long build time) | `-DTrilinos_ENABLE_ALL_PACKAGES:BOOL=ON` |
| Development tier | Tier 1 + frequently used Tier 2 | Enable packages explicitly or via `-DTrilinos_ENABLE_TESTS:BOOL=ON` |

**Package tier reference:**

- **Tier 1:** Core infrastructure (Teuchos, Kokkos, RTOp)
- **Tier 2:** Numerical kernels (Belos, Ifpack, Muelu)
- **Tier 3:** Application-specific solvers (Piro, Rythmos)
- See doc 05 for detailed tier routing

---

### Step 4: Build Type Selection

**Choose build type:**

| Requirement | Build Type | CMake Option |
|-------------|------------|--------------|
| Development | Debug with symbols | `-DCMAKE_BUILD_TYPE:STRING=Debug` |
| Production | Release with optimization | `-DCMAKE_BUILD_TYPE:STRING=Release` |
| Profiling | Release with debug info | `-DCMAKE_BUILD_TYPE:STRING=RelWithDebInfo` |

**Recommendation:** Start with `Release` or `RelWithDebInfo` for initial builds; switch to `Debug` only when troubleshooting.

---

### Step 5: Escalation Paths

**If configure fails:**

1. **Review CMake error output** → Look for missing dependencies, TPLs, or compiler issues
2. **Check environment variables** → Verify compiler, linker, and MPI paths
3. **Consult troubleshooting matrix** → Doc 06 for configure/build failure patterns
4. **Review error pattern router** → Doc 18 for stage-based failure isolation

**If build fails:**

1. **Check compile errors** → Look for header, template, or dependency issues
2. **Verify package dependencies** → Some packages require others enabled
3. **Review build log** → Search for "error:" or "fatal error:" strings
4. **Escalate to error pattern router** → Doc 18 for systematic triage

**If install fails:**

1. **Check install prefix permissions** → Verify write access to target directory
2. **Review CMAKE_INSTALL_PREFIX** → Ensure path exists and is accessible
3. **Verify file conflicts** → Check for existing installations
4. **Consult install verification checklist** → Doc 29

---

## Quick Start Presets

### Minimal Serial Build (Smoke Test)

```bash
cmake \
  -DCMAKE_BUILD_TYPE:STRING=Release \
  -DCMAKE_INSTALL_PREFIX:PATH=$HOME/trilinos-install \
  -DTrilinos_ENABLE_ALL_PACKAGES:BOOL=OFF \
  -DTrilinos_ENABLE_Teuchos:BOOL=ON \
  -DTrilinos_ENABLE_Kokkos:BOOL=ON \
  -DTrilinos_ENABLE_Tpetra:BOOL=ON \
  -DTPL_ENABLE_MPI:BOOL=ON \
  -DMPI_EXEC:STRING=/usr/bin/true \
  /path/to/trilinos/source

make -j$(nproc)
make install
```

### Standard MPI Build

```bash
cmake \
  -DCMAKE_BUILD_TYPE:STRING=Release \
  -DCMAKE_INSTALL_PREFIX:PATH=$HOME/trilinos-install \
  -DTrilinos_ENABLE_ALL_PACKAGES:BOOL=OFF \
  -DTrilinos_ENABLE_Teuchos:BOOL=ON \
  -DTrilinos_ENABLE_Kokkos:BOOL=ON \
  -DTrilinos_ENABLE_Tpetra:BOOL=ON \
  -DTrilinos_ENABLE_Belos:BOOL=ON \
  -DTrilinos_ENABLE_Ifpack2:BOOL=ON \
  -DTPL_ENABLE_MPI:BOOL=ON \
  /path/to/trilinos/source

make -j$(nproc)
make install
```

---

### Related Documentation

- `04_BUILD_INSTALL_PLAYBOOK.md` — Detailed configure/build/install procedures
- `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md` — Build profile templates
- `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md` — Failure matrix with fixes
- `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md` — Stage-based error routing
- `31_TPL_DISCOVERY_AND_PATH_HINTS.md` — TPL dependency resolution

## Validation

- Decision tree paths validated against common Trilinos build scenarios
- MPI and non-MPI configuration options tested on multiple platforms
- Escalation paths reference existing troubleshooting documentation
- Quick start presets reproduce successful builds from upstream examples

## Provenance

- `Trilinos/INSTALL.rst` — Installation procedures and options
- Official Trilinos docs: <https://trilinos.github.io/get-started.html>
- Repository examples: `Trilinos/cmake/tribits/` for TriBITS configuration
- Community build templates: Trilinos issue tracker and mailing list
