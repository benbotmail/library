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

## Owner mapping interpretation (routing policy)
- Treat CODEOWNERS as the **review-routing hint**, not strict package authority metadata.
- If a package is `(not mapped)`, route by nearest maintained package family first, then confirm maintainers via recent commits/PR history.
- When owner and package signal conflict, prefer explicit path matches in `.github/CODEOWNERS` over inferred team names.

### See also
- `05_PACKAGE_ROUTING_AND_TIERS.md` — Package-family routing and tier classification
- `16_REPO_SURFACE_MAP.md` — Repository directory map and navigation
- `17_PACKAGE_SELECTION_STRATEGY.md` — Framework for choosing initial package sets
- `12_TERMS_AND_METADATA_GLOSSARY.md` — Glossary for package and TPL terminology

## Validation
- Recompute package list from `packages/` when repository updates.
- Re-read `.github/CODEOWNERS` because ownership entries can change independently of package paths.
- For `(not mapped)` entries, verify whether ownership moved to broader wildcard patterns in CODEOWNERS before escalation.
- Cross-references validated against existing docs pack (2026-04-13).

## Provenance
- `Trilinos/packages/`
- `Trilinos/.github/CODEOWNERS`
