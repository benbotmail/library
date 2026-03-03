# Compiler and C++ Standard Alignment Guide

## Scope
Provide a practical checklist for aligning compiler selection and C++ standard settings in Trilinos configure/build workflows to reduce avoidable configuration and link failures.

## Audience
- Engineers configuring Trilinos for first-time or migrated toolchains
- LLM assistants answering build failures related to compiler/C++ standard mismatches

## Prerequisites
- Access to Trilinos configure flags and build logs
- Ability to inspect compiler versions and effective CMake cache values

## Content

### Common mismatch symptoms
- Configure-time failures reporting unsupported C++ language level
- Inconsistent feature detection between C and C++ compilers
- Link/runtime failures when downstream code uses a different C++ standard than installed Trilinos

### Alignment checklist
1. **Pick one compiler family/version line**
   - Keep C and C++ compilers from the same toolchain where possible.
2. **Set and verify the C++ standard explicitly**
   - Prefer explicit cache flags rather than implicit defaults.
3. **Avoid mixed-standard downstream integration**
   - Downstream app C++ standard should match (or be compatible with) installed Trilinos expectations.
4. **Reconfigure from a clean build directory after standard/toolchain changes**
   - Cached feature checks can mask or preserve old assumptions.

### Minimal verification commands
```bash
which cc c++
cc --version
c++ --version
```

After configure, confirm effective settings in cache/logs:
- active compiler paths
- active C++ standard-related cache entries
- first decisive language/feature error if configure fails

### Safe recovery pattern
1. Remove stale build directory.
2. Re-run configure with explicit compiler paths.
3. Set explicit C++ standard-related options in configure.
4. Build a minimal package set first, then expand.

### Handoff snippet for unresolved cases
```text
Compiler C/C++:
Requested C++ standard settings:
Effective cache values:
First decisive compiler/language error:
Minimal configuration that reproduces:
```

## Validation
- Checklist covers configure, build, and downstream compatibility touchpoints.
- Guidance emphasizes explicit settings and clean-cache reconfiguration.

## Provenance
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/13_CMAKE_FLAG_QUICK_REFERENCE.md`
- `library/Trilinos/14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`
- `library/Trilinos/36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- `library/Trilinos/40_BUILD_PROFILE_SELECTION_MATRIX.md`
- Trilinos upstream repository docs: <https://github.com/trilinos/Trilinos>
- Official Trilinos documentation index: <https://trilinos.github.io/documentation.html>
