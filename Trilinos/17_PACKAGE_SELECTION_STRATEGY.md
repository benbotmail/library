# Trilinos Package Selection Strategy

## Scope
Decision framework for choosing an initial Trilinos package set before scaling to broader configurations.

## Audience
- Engineers planning a first practical Trilinos build
- LLM systems recommending package enables based on user goals

## Prerequisites
- Familiarity with Trilinos package naming (`Trilinos_ENABLE_<Package>`)
- Access to package routing and troubleshooting docs in this collection

## Content

### Why package selection first
Starting with targeted package enables reduces dependency surface, shortens iteration cycles, and lowers initial configure failure risk compared to enabling all packages immediately.

### Goal-oriented starting sets

#### Baseline linear algebra path
- `Teuchos`
- `Tpetra`

Use when the goal is a minimal functional install and downstream integration validation.

#### Solver-oriented path
- `Teuchos`, `Tpetra`
- `Belos`, `Ifpack2`, `Amesos2`, `MueLu`

Use when iterative/direct solver workflows are the main objective.

#### Multiphysics-adjacent path
- `Intrepid2`, `Phalanx`, `Panzer`, `STK`, `Tempus`

Use for discretization, assembly, and coupled simulation workflows.

### Expansion sequence
1. Start with minimal baseline packages.
2. Validate configure/build/install.
3. Add one package family at a time.
4. Re-run tests and capture first failing dependency signal.
5. Only then evaluate broad package enable.

### Anti-pattern to avoid
- Enabling `Trilinos_ENABLE_ALL_PACKAGES=ON` before validating toolchain, MPI mode, and TPL readiness.

### Quick decision checklist
Use this checklist to select a starting package set in under 60 seconds:

1. **Do you need distributed-memory parallelism?**
   - Yes → Include `Tpetra`, `Belos`, `Ifpack2` (MPI required)
   - No → Serial/shared-memory builds may omit MPI-dependent packages

2. **Is your primary goal linear algebra only?**
   - Yes → Start with `Teuchos`, `Tpetra` (or `Epetra` for legacy)
   - No → Add solver packages (`Belos`, `Amesos2`, `MueLu`) as needed

3. **Do you need discretization/FE support?**
   - Yes → Include `Intrepid2`, `Phalanx`, `STK`
   - No → Skip these to reduce TPL dependencies

4. **Are you integrating with an existing application?**
   - Yes → Match packages to what your app actually calls via `find_package(Trilinos ...)`
   - No → Start minimal and expand incrementally

### Common starting combinations
| Use case | Recommended packages |
|---|---|
| Minimal smoke test | `Teuchos` |
| Basic linear algebra | `Teuchos`, `Tpetra` |
| Iterative solvers | `Teuchos`, `Tpetra`, `Belos`, `Ifpack2` |
| Direct solvers | Add `Amesos2` (requires TPL like SuperLU) |
| Multigrid preconditioning | Add `MueLu` |

### Cross-reference map
- Build mechanics: `04_BUILD_INSTALL_PLAYBOOK.md`
- TPL readiness: `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
- Failure handling: `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
- Package routing context: `05_PACKAGE_ROUTING_AND_TIERS.md`

## Validation
- Confirm package names against `PackagesList.cmake`.
- Confirm selected set matches dependency and environment constraints before configure.

## Provenance
- `Trilinos/PackagesList.cmake`
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/05_PACKAGE_ROUTING_AND_TIERS.md`
- `library/Trilinos/06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
- `library/Trilinos/09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
