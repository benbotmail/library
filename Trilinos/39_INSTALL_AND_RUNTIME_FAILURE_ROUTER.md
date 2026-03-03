# Trilinos Install and Runtime Failure Router

## Scope
Symptom-to-fix router for failures that occur **after compile succeeds**, specifically during install, first execution, or runtime library loading.

## When to use
Use this page when configure/build passed, but install or runtime behavior fails in local or downstream environments.

## Symptom → likely cause → first action

### 1) `install` step fails with permission/path errors
**Likely cause**
- Install prefix is not writable, or target path is invalid for current user.

**First action**
- Choose a writable install prefix.
- Re-run install from the same successful build tree.

---

### 2) Install succeeds, downstream cannot find Trilinos package config
**Likely cause**
- Downstream CMake search path does not include install metadata location.

**First action**
- Set `Trilinos_DIR` to installed `TrilinosConfig*.cmake` directory or set `CMAKE_PREFIX_PATH` to install prefix.

---

### 3) Runtime error: shared library not found (`lib*.so` missing)
**Likely cause**
- Runtime loader path does not include Trilinos/TPL shared-library directories.

**First action**
- Configure runtime linker path strategy (`LD_LIBRARY_PATH` or rpath-based approach) for target environment.

---

### 4) Runtime crashes or unresolved symbols despite successful link
**Likely cause**
- ABI/toolchain mismatch between Trilinos build and downstream application environment.

**First action**
- Align compiler family/version and MPI mode between Trilinos and downstream app; rebuild one side if needed.

---

### 5) MPI job starts but fails immediately at runtime
**Likely cause**
- Mixed MPI stacks or incompatible MPI runtime environment.

**First action**
- Verify consistent MPI implementation and wrappers across Trilinos build, downstream build, and runtime launcher environment.

## Fast acceptance checklist
1. Install prefix is writable and complete.
2. `TrilinosConfig*.cmake` exists in install tree.
3. Downstream smoke project configures and links against install.
4. Runtime library path/rpath strategy validated.
5. MPI consistency verified (if MPI build).

## Cross references
- `29_INSTALL_VERIFICATION_CHECKLIST.md`
- `27_DOWNSTREAM_INTEGRATION_FAILURE_PATTERNS.md`
- `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`
- `04_BUILD_INSTALL_PLAYBOOK.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- `Trilinos/demos/simpleBuildAgainstTrilinos/README.md`
- Companion troubleshooting pages in local `library/Trilinos/` docs
