# Build Profile Selection Matrix

At-a-glance goal-to-profile matrix for choosing safe starting build configurations.

## Scope
Quick-reference matrix mapping common user goals to recommended starting build profiles. Helps avoid first-attempt configure failures by selecting conservative, goal-appropriate configurations.

## Audience
- Engineers new to Trilinos seeking a working first build
- Users needing to match build configuration to specific use cases
- LLM systems recommending build configurations for user intents

## Prerequisites
- Basic understanding of Trilinos as a framework of packages
- Familiarity with CMake configuration options
- Awareness of downstream application requirements (MPI, shared/static linking)

## Content

### Goal-to-Profile Matrix

| User Goal | Recommended Profile | Key CMake Options | Notes |
|-----------|---------------------|-------------------|-------|
| **First-time install, explore codebase** | `Minimal Serial` | `-DTrilinos_ENABLE_TESTS=OFF` `-DTrilinos_ENABLE_EXAMPLES=OFF` | Serial only, minimal packages, fast build (~10-30 min) |
| **Learning Tpetra/Kokkos** | `Kokkos-Tpetra Serial` | `-DTrilinos_ENABLE_Kokkos=ON` `-DTrilinos_ENABLE_Tpetra=ON` | Add Kokkos and Tpetra to minimal profile |
| **Debug local development** | `Debug Serial` | `-DCMAKE_BUILD_TYPE=Debug` `-DTrilinos_ENABLE_TESTS=ON` | Debug symbols, tests enabled for TDD workflows |
| **Downstream app integration testing** | `Shared Libraries + MPI` | `-DBUILD_SHARED_LIBS=ON` `-DTPL_ENABLE_MPI=ON` | Shared libs for app linking, MPI required by many packages |
| **Benchmarking / performance work** | `Release MPI + Optimized` | `-DCMAKE_BUILD_TYPE=Release` `-DTPL_ENABLE_MPI=ON` `-DTrilinos_ENABLE_CHECKED_STL=OFF` | Optimized builds, disable debug checks for speed |
| **Package exploration (specific domain)** | `Domain-Specific Tiered` | See package tier section below | Enable packages by tier/family to manage dependency depth |
| **CI / reproducible builds** | `Preset-based Minimal` | Use CMake presets (see `58_CMAKE_PRESETS_ADOPTION_GUIDE.md`) | Presets ensure reproducibility across environments |
| **Trilinos testing / validation** | `Full Tests + MPI` | `-DTrilinos_ENABLE_TESTS=ON` `-DTrilinos_ENABLE_EXAMPLES=ON` `-DTPL_ENABLE_MPI=ON` | All tests and examples; long build time (1-4+ hours) |

### Conservative Starter Profiles

#### Minimal Serial (Fastest First Build)
```bash
cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DTPL_ENABLE_MPI=OFF \
  -DTrilinos_ENABLE_TESTS=OFF \
  -DTrilinos_ENABLE_EXAMPLES=OFF \
  -DTrilinos_ENABLE_ALL_PACKAGES=OFF \
  <trilinos-source-dir>
```

#### Kokkos-Tpetra Serial (Learn Core APIs)
```bash
cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DTPL_ENABLE_MPI=OFF \
  -DTrilinos_ENABLE_TESTS=ON \
  -DTrilinos_ENABLE_Kokkos=ON \
  -DTrilinos_ENABLE_Tpetra=ON \
  <trilinos-source-dir>
```

#### MPI + Shared (Downstream App Integration)
```bash
cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DTPL_ENABLE_MPI=ON \
  -DBUILD_SHARED_LIBS=ON \
  -DTrilinos_ENABLE_TESTS=OFF \
  -DTrilinos_ENABLE_EXAMPLES=OFF \
  <trilinos-source-dir>
```

### Package Tier Expansion Strategy

When enabling packages beyond minimal sets, follow tier-based expansion to manage complexity:

| Tier | Example Packages | When to Use | Risk Level |
|------|------------------|-------------|------------|
| **Tier 1 (Core基础设施)** | Kokkos, Tpetra, Teuchos | Starting any distributed computing work | Low - stable, well-documented |
| **Tier 2 (Linear Algebra)** | Ifpack2, Muelu, Amesos2 | Preconditioning, solvers, domain decomposition | Medium - may have TPL dependencies |
| **Tier 3 (Application-Specific)** | Stokhos, Panzer, NOX | Uncertainty quantification, PDE solves, nonlinear solvers | Higher - deeper TPL requirements |

**Expansion pattern:** Start with Tier 1 core, then add Tier 2 packages as needed. Avoid enabling Tier 3 packages before confirming Tier 2 builds succeed.

### Build Time Estimates

| Profile | Packages | Build Time (16-core) | Notes |
|---------|----------|---------------------|-------|
| Minimal Serial | ~5 | 10-30 min | Best for first success validation |
| Kokkos-Tpetra Serial | ~15 | 30-60 min | Core API exploration |
| MPI + Shared | ~20-30 | 45-90 min | Downstream integration prep |
| Full Tier 1+2 | ~50-80 | 2-4 hours | Heavy dependency resolution |
| All packages | 200+ | 4-8+ hours | Not recommended for first builds |

### Common Pitfalls

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Enabling `Trilinos_ENABLE_ALL_PACKAGES=ON` on first attempt | Configure fails or build takes hours | Start with explicit package lists or tier-based expansion |
| Mixing Debug + Release libraries across Trilinos and downstream app | Runtime symbol resolution failures | Keep `CMAKE_BUILD_TYPE` consistent across all projects |
| Skipping `-DTPL_ENABLE_MPI` when downstream app expects MPI | Linker errors for MPI symbols | Align MPI enable/disable with downstream app requirements |
| Building static libs when downstream app expects shared (or vice versa) | Linker or runtime loader failures | Set `-DBUILD_SHARED_LIBS` consistently |
| Ignoring TPL warnings | Build succeeds but tests fail or runtime errors occur | Check `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md` for TPL requirements |

### When to Switch Profiles

| From | To | Trigger |
|------|-----|---------|
| Minimal Serial → Kokkos-Tpetra | Need Tpetra/Kokkos APIs | User requests distributed computing or GPU work |
| Debug → Release | Ready for benchmarks/tests | Build stability confirmed, need performance |
| Serial → MPI | Downstream app uses MPI | App requires MPI communication or expects parallel builds |
| Static → Shared | Downstream app links against shared libs | Linker errors on missing `.so` files, or app uses dynamic loading |

### Related Documentation

- `04_BUILD_INSTALL_PLAYBOOK.md` — Step-by-step configure/build/install workflows
- `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md` — Detailed profile templates and customization
- `17_PACKAGE_SELECTION_STRATEGY.md` — Framework for choosing initial package sets
- `34_BUILD_INSTALL_DOC_NAVIGATION_MAP.md` — Intent-to-document router for deeper troubleshooting

### Usage Validation Checklist

- [ ] Profile matches user's stated goal (explore, integrate, benchmark, etc.)
- [ ] MPI enable/disable consistent with downstream app requirements
- [ ] `BUILD_SHARED_LIBS` setting matches downstream linking expectations
- [ ] `CMAKE_BUILD_TYPE` appropriate for development stage (Debug vs Release)
- [ ] TPL requirements reviewed and paths configured (if applicable)
- [ ] Build time estimate communicated to user before starting

## Validation

- Matrix entries validated against upstream Trilinos build configurations
- Build time estimates based on typical workstation builds (16-core, modern CPU, SSD)
- Profile recommendations tested across common use cases (exploration, integration, benchmarking)
- Common pitfalls identified from community issue reports and mailing list discussions
- Related documentation cross-references verified for accuracy and relevance

## Provenance

- Build configuration patterns from `Trilinos/INSTALL.rst`
- Package tier classifications inferred from repository structure and common use patterns
- Build time estimates based on typical workstation builds (16-core, modern CPU, SSD storage)
- MPI and shared library guidance from `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
- CMake preset workflows from `58_CMAKE_PRESETS_ADOPTION_GUIDE.md`
