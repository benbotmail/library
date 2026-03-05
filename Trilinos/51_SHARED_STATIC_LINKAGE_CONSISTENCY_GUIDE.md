# Shared vs Static Linkage Consistency Guide

## Scope
Provide a practical guide for avoiding and diagnosing Trilinos integration failures caused by mixed shared/static linkage assumptions across Trilinos, TPLs, and downstream applications.

## Audience
- Engineers building Trilinos for downstream CMake consumers
- LLM assistants triaging link/runtime issues with library-type mismatches

## Prerequisites
- Trilinos build/install completed or in progress
- Access to configure flags, linker errors, and downstream CMake config

## Content

### Typical mismatch symptoms
- Undefined references during downstream link despite found Trilinos package config
- Runtime loader errors after a successful link (especially with shared builds)
- Duplicate symbol or ODR-like issues when mixing static and shared artifacts unintentionally

### Consistency rules
1. **Choose a linkage strategy up front**
   - Decide shared-first or static-first for Trilinos and keep downstream expectations aligned.
2. **Keep TPL linkage compatible with Trilinos build strategy**
   - Avoid implicit mixing where Trilinos is one mode but critical dependencies are effectively consumed in another incompatible mode.
3. **Downstream should mirror installed Trilinos assumptions**
   - Ensure downstream CMake options and link interfaces follow the installed Trilinos package configuration.
4. **Do not carry cache state across linkage strategy changes**
   - Reconfigure from a clean build directory when switching shared/static settings.

### Minimal checklist
- `BUILD_SHARED_LIBS` value is explicit and intentional.
- Install prefix corresponds to the intended linkage mode.
- Downstream project is using the correct Trilinos install and package config files.
- Runtime library search path is configured when shared libraries are used.

### Safe recovery sequence
1. Clean build directory.
2. Reconfigure Trilinos with explicit linkage mode.
3. Rebuild/reinstall.
4. Reconfigure downstream app against that exact install.
5. Re-test minimal downstream target before full workload.

### Handoff snippet
```text
Trilinos linkage mode (shared/static):
Downstream expected mode:
Critical TPL linkage notes:
Install prefix in use:
First decisive link/runtime error:
```

## Validation
- Guidance covers both build-time and runtime consequences of linkage mismatch.
- Recovery path is deterministic and cache-safe.

## Provenance
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/27_DOWNSTREAM_INTEGRATION_FAILURE_PATTERNS.md`
- `library/Trilinos/29_INSTALL_VERIFICATION_CHECKLIST.md`
- `library/Trilinos/39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`
- `library/Trilinos/47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`
- Trilinos upstream repository docs: <https://github.com/trilinos/Trilinos>
- Official Trilinos documentation index: <https://trilinos.github.io/documentation.html>
