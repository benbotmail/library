# Trilinos Build/Install Minimum Context Schema

## Scope
Minimal input schema for collecting the **least information required** to provide accurate Trilinos build/install guidance.

## Why this matters
Build advice quality drops sharply when key context is missing. This schema standardizes intake so LLM responses remain actionable and low-risk.

## Required fields (minimum viable context)
1. **Goal**
   - `first_success` | `mpi_integration` | `downstream_build` | `troubleshoot_configure` | `troubleshoot_runtime` | `ci_speedup`

2. **Platform/toolchain**
   - OS/distribution
   - CMake version
   - C/C++ compiler family + version

3. **MPI intent**
   - `mpi_required: true|false|unknown`

4. **Package scope intent**
   - `minimal` | `selected` | `broad`
   - If `selected`: package names list

5. **Install target**
   - intended install prefix path

## Conditionally required fields
- If troubleshooting configure/build:
  - first decisive error block (exact text)
  - configure command used
- If downstream integration:
  - downstream `find_package`/configure snippet
  - how Trilinos discovery path is being passed (`Trilinos_DIR`/`CMAKE_PREFIX_PATH`)
- If runtime failure:
  - exact runtime error text
  - shared/static build mode if known

## JSON example
```json
{
  "goal": "first_success",
  "platform": {
    "os": "ubuntu-24.04",
    "cmake": "3.28.3",
    "c_compiler": "gcc-13",
    "cxx_compiler": "g++-13"
  },
  "mpi_required": false,
  "package_scope": {
    "mode": "minimal",
    "selected": []
  },
  "install_prefix": "/opt/trilinos-local"
}
```

## Output contract (recommended)
When this schema is complete, responses should return:
1. command-first plan,
2. smallest safe fallback if step fails,
3. verification/acceptance checks,
4. next-step expansion path.

## Cross references
- `26_BUILD_INSTALL_DECISION_TREE.md`
- `32_30_MINUTE_FIRST_SUCCESS_PATH.md`
- `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- `39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`
- `41_RETRIEVAL_ENTRYPOINTS_BUILD_INSTALL.md`

## Provenance
- Intake requirements synthesized from recurring dependencies in local `library/Trilinos/` build/install and troubleshooting documents.
