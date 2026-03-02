# Trilinos TPL Discovery and Path Hints

## Scope
Quick-reference guidance for making third-party libraries (TPLs) discoverable during Trilinos configure, with emphasis on reducing trial-and-error.

## When to use
Use this page when configure fails due to missing TPLs, unresolved include/library paths, or ambiguous dependency detection behavior.

## Core principles
1. **Prefer explicit paths over implicit environment assumptions** for reproducibility.
2. **Add TPLs incrementally** when possible; broad package enables can multiply TPL requirements.
3. **Keep compiler/MPI consistency** across Trilinos and TPL builds to avoid ABI/link surprises.

## Practical hint hierarchy

### 1) Start from package scope
- If only a subset of packages is needed, enable only those packages first.
- This limits TPL surface area and speeds diagnosis.

### 2) Provide stable root hints
- Point Trilinos/CMake to known install roots using configure options and prefix paths.
- Keep these hints in profile-specific configure scripts.

### 3) Verify detection in configure output
- Treat configure logs as primary evidence for what was found vs skipped.
- Resolve one missing dependency class at a time.

### 4) Escalate to broad enable only after baseline succeeds
- Once core package/TPL combinations configure cleanly, widen package scope.

## Common failure patterns

### Pattern A: “Required TPL not found” after enabling many packages
- **Cause:** Package scope exceeded available local TPL set.
- **First move:** Reduce package set, then add TPL hints incrementally.

### Pattern B: Include paths found, library link still fails
- **Cause:** Partial TPL discovery or incompatible library variant.
- **First move:** Re-check compiler family/version consistency and library locations.

### Pattern C: MPI/TPL interaction failures
- **Cause:** Mixed MPI/non-MPI stacks or wrapper mismatch.
- **First move:** Standardize toolchain and ensure MPI-aware dependencies are aligned.

## Minimal TPL triage checklist
- Confirm required package list is intentional.
- Confirm toolchain consistency (compiler + MPI mode).
- Confirm install roots for required TPLs are reachable and explicit.
- Reconfigure in a clean out-of-source build directory.
- Expand scope only after a passing baseline configure.

## Cross references
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
- `13_CMAKE_FLAG_QUICK_REFERENCE.md`
- `17_PACKAGE_SELECTION_STRATEGY.md`
- `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- Trilinos package/TPL behavior as reflected by configure-time diagnostics and companion docs in this pack
