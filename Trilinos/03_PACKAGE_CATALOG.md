# Trilinos Package Catalog

## Scope
Catalog of package directories under `Trilinos/packages/`, with CODEOWNERS mapping when available.

## Audience
- Engineers locating package roots quickly
- LLM systems routing package-specific queries

## Prerequisites
- Local or remote access to the Trilinos repository tree
- `.github/CODEOWNERS` for owner mapping

## Content

### Package count
Total packages detected under `packages/`: **43**

### Package to owner mapping

| Package | Owner (CODEOWNERS) |
|---|---|
| `PyTrilinos2` | @trilinos/pytrilinos2 |
| `TrilinosInstallTests` | (not mapped) |
| `adelus` | (not mapped) |
| `amesos2` | @trilinos/amesos2 |
| `anasazi` | @trilinos/anasazi |
| `belos` | @trilinos/belos |
| `common` | (not mapped) |
| `compadre` | (not mapped) |
| `epetra` | @trilinos/epetra |
| `external` | (not mapped) |
| `framework` | @trilinos/framework |
| `galeri` | (not mapped) |
| `ifpack2` | @trilinos/ifpack2 |
| `intrepid2` | @trilinos/intrepid2 |
| `kokkos` | @trilinos/kokkos |
| `kokkos-kernels` | @trilinos/kokkos-kernels |
| `krino` | (not mapped) |
| `minitensor` | (not mapped) |
| `muelu` | @trilinos/muelu |
| `nox` | @trilinos/nox |
| `pamgen` | @trilinos/pamgen |
| `panzer` | @trilinos/panzer |
| `percept` | (not mapped) |
| `phalanx` | @trilinos/phalanx |
| `piro` | @trilinos/piro |
| `rol` | @trilinos/rol |
| `rtop` | @trilinos/rtop |
| `sacado` | @trilinos/sacado |
| `seacas` | @trilinos/seacas |
| `shards` | @trilinos/shards |
| `shylu` | @trilinos/shylu |
| `stk` | @trilinos/stk |
| `stokhos` | @trilinos/stokhos |
| `stratimikos` | @trilinos/stratimikos |
| `teko` | @trilinos/teko |
| `tempus` | @trilinos/tempus |
| `teuchos` | @trilinos/teuchos |
| `thyra` | @trilinos/thyra |
| `tpetra` | @trilinos/tpetra |
| `trilinoscouplings` | @trilinos/trilinoscouplings |
| `xpetra` | @trilinos/xpetra |
| `zoltan` | @trilinos/zoltan |
| `zoltan2` | @trilinos/zoltan2 |

## Validation
- Recompute package list from `packages/` when repository updates.
- Re-read `.github/CODEOWNERS` because ownership entries can change independently of package paths.

## Provenance
- `Trilinos/packages/`
- `Trilinos/.github/CODEOWNERS`
