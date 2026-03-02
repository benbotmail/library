# Trilinos Install Verification Checklist

## Scope
Post-install verification checklist to confirm a Trilinos installation is usable for downstream CMake projects.

## When to use
Run this immediately after `make install`/`ninja install`, before handing the install prefix to users or CI jobs.

## Checklist

1. **Install prefix exists and is populated**
   - Confirm expected directories exist (for example `include/`, `lib*/`, and CMake package metadata paths).

2. **Trilinos CMake package metadata is present**
   - Verify `TrilinosConfig*.cmake` files exist under the install tree.
   - If missing, downstream `find_package(Trilinos)` will fail.

3. **Package expectations match installed configuration**
   - Cross-check required downstream packages against what was enabled during Trilinos configure.

4. **Toolchain consistency is documented**
   - Record compiler family/version and MPI/non-MPI mode used for the install.
   - Downstream projects should use compatible compilers/wrappers.

5. **Shared-library runtime path is handled (if shared build)**
   - Ensure runtime loader path/rpath strategy is defined for target environment.

6. **Downstream smoke test passes**
   - Build a minimal downstream example against the installed prefix.
   - Treat this as required acceptance, not optional.

7. **Profile/config script captured for reproducibility**
   - Save the exact configure invocation (`do-configure` or equivalent).

## Failure handling
- Missing config files → re-check install step and install prefix.
- Downstream discovery failure → set explicit `Trilinos_DIR` or `CMAKE_PREFIX_PATH`.
- Link/runtime failures → validate package enable set, MPI/compiler consistency, and runtime loader path.

## Cross references
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
- `13_CMAKE_FLAG_QUICK_REFERENCE.md`
- `27_DOWNSTREAM_INTEGRATION_FAILURE_PATTERNS.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- Installed Trilinos package metadata in install tree (`TrilinosConfig*.cmake`)
