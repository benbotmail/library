# Trilinos Package Routing & Tier Signals

## Scope
Fast routing guide for LLM/human navigation across Trilinos package space.

## Key fact
`PackagesList.cmake` defines **71 package entries** in TriBITS repository definitions (including framework/test/external entries).

## Tier markers seen in `PackagesList.cmake`
- `PT` — primary/stable package tier in practice for most user-facing paths.
- `ST` — secondary/supplemental tier (often optional or specialized in typical workflows).
- `EX` — experimental/external-oriented tier entries.

> Note: These labels are used by Trilinos/TriBITS configuration logic; treat this page as a routing aid, not policy.

## High-priority package families for first docs wave

### Solver / preconditioner / linear algebra ecosystem
- Belos
- Ifpack2
- Amesos2
- MueLu
- Teko
- Stratimikos
- NOX
- ROL
- Piro

### Core foundations
- Teuchos
- Tpetra
- Thyra
- Xpetra
- Sacado
- Kokkos
- KokkosKernels

### Discretization / FE / multiphysics-adjacent
- Intrepid2
- Phalanx
- Panzer
- STK
- Shards
- Tempus
- TrilinosCouplings

### Partitioning / load balancing
- Zoltan
- Zoltan2

## Recommended documentation order (ROI-first)
1. Build/install and package enable strategy (done in `04_BUILD_INSTALL_PLAYBOOK.md`)
2. Core foundations (Teuchos/Tpetra/Kokkos family)
3. Solver stack (Belos/Ifpack2/Amesos2/MueLu)
4. FE/multiphysics path (Intrepid2/Phalanx/Panzer/STK)
5. Specialty and experimental entries

## Retrieval tags (proposed)
Use these tags in per-package pages for consistent retrieval:
- `family:core`
- `family:solver`
- `family:preconditioner`
- `family:discretization`
- `family:multiphysics`
- `family:partitioning`
- `tier:PT|ST|EX`

## Provenance
- `Trilinos/PackagesList.cmake`
- `Trilinos/.github/CODEOWNERS`
- Official docs index: <https://trilinos.github.io/documentation.html>
