# Trilinos Getting Started Route

## Scope
Entry-point navigation for new users who need to move from first read to first successful build and downstream integration.

## Audience
- Engineers new to Trilinos
- LLM systems deciding which Trilinos docs page to retrieve first

## Prerequisites
- Access to this Trilinos docs pack
- Access to upstream Trilinos repository and official docs site

## Content

### Recommended reading order
1. `02_SOURCE_OF_TRUTH_MAP.md`
   - Establishes source authority and confidence order.
2. `04_BUILD_INSTALL_PLAYBOOK.md`
   - Provides baseline configure/build/install flows.
3. `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
   - Pre-checks environment and dependency expectations.
4. `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
   - Resolves common configure/build failures quickly.
5. `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
   - Integrates a separate CMake project with installed Trilinos.

### Task-based routing
- **Need first successful Trilinos install:**
  - Start with `04_BUILD_INSTALL_PLAYBOOK.md`.
- **Need to understand dependency readiness before configure:**
  - Use `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`.
- **Configure/build fails with missing libs or MPI errors:**
  - Use `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`.
- **Need to build your own app against Trilinos:**
  - Use `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`.
- **Need package ownership and package map:**
  - Use `03_PACKAGE_CATALOG.md` and `05_PACKAGE_ROUTING_AND_TIERS.md`.
- **Need contribution/security process:**
  - Use `08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md`.

### Minimal first-session checklist
1. Confirm CMake and compiler prerequisites.
2. Choose MPI or non-MPI build path.
3. Choose targeted packages before enabling all packages.
4. Save configure command in a reusable script.
5. Validate install with a downstream integration test.

## Validation
- Confirm each route target file exists in `library/Trilinos/`.
- Confirm reading order aligns with `00_INDEX.md` and source-of-truth hierarchy.

## Provenance
- `library/Trilinos/00_INDEX.md`
- `library/Trilinos/02_SOURCE_OF_TRUTH_MAP.md`
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
- `library/Trilinos/07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
- `library/Trilinos/09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
- `Trilinos/README.md`
- Official docs index: <https://trilinos.github.io/documentation.html>
