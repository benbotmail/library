# MPI Wrapper and ABI Consistency Checklist

## Scope
Provide a focused checklist for diagnosing Trilinos build/install/runtime failures caused by inconsistent MPI wrappers, mixed compiler families, or ABI mismatches.

## Audience
- Engineers building Trilinos with MPI-enabled configurations
- LLM assistants triaging linker/runtime failures involving MPI stacks

## Prerequisites
- MPI-enabled toolchain installed
- Ability to inspect compiler and MPI wrapper identities
- Access to configure/build logs and CMake cache

## Content

### Typical failure signals
- Link errors with unresolved MPI symbols
- Runtime launch failures under `mpirun`/`mpiexec` despite successful build
- Crashes or undefined behavior when mixing binaries built with different compiler/MPI stacks

### Consistency checks (in order)
1. **Compiler family alignment**
   - Ensure C/C++/Fortran compilers belong to one coherent toolchain family/version line.
2. **MPI wrapper provenance**
   - Verify `mpicc`, `mpicxx`, `mpifort` resolve to the intended MPI distribution.
   - Confirm wrapper-reported backend compiler matches chosen compiler family.
3. **Single MPI implementation across dependencies**
   - Avoid mixing OpenMPI and MPICH-family artifacts in one build/install path.
4. **CMake cache sanity**
   - Confirm cached compiler/wrapper paths match current environment.
   - If environment changed, wipe build dir and reconfigure.
5. **Runtime launcher/path consistency**
   - Use launcher from same MPI stack used at build time.
   - Ensure runtime library paths resolve to the same stack (avoid shadowing by system defaults).

### Quick command checklist
```bash
which cc c++ mpicc mpicxx mpifort
cc --version
c++ --version
mpicc --version
mpicxx --version
```

Optional wrapper introspection (implementation-dependent):
```bash
mpicc -show
mpicxx -show
```

### Safe recovery play
1. Remove old build directory.
2. Reconfigure with explicit compiler and MPI wrapper paths.
3. Rebuild from scratch.
4. Re-run minimal MPI smoke test before broad package expansion.

### Reporting template snippet
```text
Compiler family/version:
MPI implementation/version:
Wrapper paths (mpicc/mpicxx/mpifort):
Wrapper backend compiler evidence:
Configure flags for compilers/MPI:
First decisive link/runtime error:
```

## Validation
- Checklist isolates wrapper/compiler/launcher mismatches before deeper package-level debugging.
- Steps are compatible with configure/build/runtime triage flow.

## Provenance
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
- `library/Trilinos/18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`
- `library/Trilinos/39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`
- `library/Trilinos/43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md`
- Trilinos upstream repository docs: <https://github.com/trilinos/Trilinos>
- Official Trilinos documentation index: <https://trilinos.github.io/documentation.html>
