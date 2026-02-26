# Trilinos Package Routing and Tier Signals

## Scope
Routing guide for navigating Trilinos package families and TriBITS tier markers.

## Audience
- Engineers deciding where to start in the Trilinos package ecosystem
- LLM systems routing queries to the right package families

## Prerequisites
- Basic familiarity with Trilinos package-oriented architecture
- Access to repository metadata files (`PackagesList.cmake`, `CODEOWNERS`)

## Content

### Package entry count in TriBITS definitions
`PackagesList.cmake` defines **71 package entries** in `TRIBITS_REPOSITORY_DEFINE_PACKAGES(...)` (including framework/test/external entries).

### Tier markers from `PackagesList.cmake`
- `PT` — primary tier entries commonly central to user-facing workflows.
- `ST` — secondary/supplemental entries, often more specialized.
- `EX` — experimental/external-oriented entries.

These labels are build-system metadata signals and should be treated as routing hints.

### High-priority package families

#### Solver, preconditioner, and linear algebra ecosystem
- Belos
- Ifpack2
- Amesos2
- MueLu
- Teko
- Stratimikos
- NOX
- ROL
- Piro

#### Core foundations
- Teuchos
- Tpetra
- Thyra
- Xpetra
- Sacado
- Kokkos
- KokkosKernels

#### Discretization and multiphysics-adjacent
- Intrepid2
- Phalanx
- Panzer
- STK
- Shards
- Tempus
- TrilinosCouplings

#### Partitioning and load balancing
- Zoltan
- Zoltan2

### Recommended reading order
1. `04_BUILD_INSTALL_PLAYBOOK.md`
2. Core foundations (Teuchos/Tpetra/Kokkos family)
3. Solver stack (Belos/Ifpack2/Amesos2/MueLu)
4. Multiphysics path (Intrepid2/Phalanx/Panzer/STK)
5. Specialized and experimental entries

### Retrieval tag set for package pages
- `family:core`
- `family:solver`
- `family:preconditioner`
- `family:discretization`
- `family:multiphysics`
- `family:partitioning`
- `tier:PT|ST|EX`

## Validation
- Package and tier assertions should be verified directly against `PackagesList.cmake`.
- Ownership mappings should be cross-checked against `.github/CODEOWNERS`.

## Provenance
- `Trilinos/PackagesList.cmake`
- `Trilinos/.github/CODEOWNERS`
- Official docs index: <https://trilinos.github.io/documentation.html>
