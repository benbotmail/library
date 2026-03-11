# Trilinos LLM Query Routing Hints

## Scope
Routing hints for mapping common Trilinos user questions to the most relevant documents in this collection.

## Audience
- LLM systems answering Trilinos questions with retrieval
- Engineers designing prompt routers over this docs pack

## Prerequisites
- Access to `library/Trilinos/00_INDEX.md`
- Familiarity with package-oriented structure in Trilinos

## Content

### Routing map by query intent

- **"How do I install/build Trilinos?"**
  - Primary: `04_BUILD_INSTALL_PLAYBOOK.md`
  - Secondary: `56_PLATFORM_BASELINE_AND_TOOLCHAIN_MINIMUMS.md`, `55_KNOWN_GOOD_STARTER_CONFIGS.md`

- **"What should I check before first configure?"**
  - Primary: `56_PLATFORM_BASELINE_AND_TOOLCHAIN_MINIMUMS.md`
  - Secondary: `35_PRECONFIGURE_ENVIRONMENT_CHECKLIST.md`

- **"Why does configure fail with missing dependencies?"**
  - Primary: `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
  - Secondary: `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`, `31_TPL_DISCOVERY_AND_PATH_HINTS.md`

- **"Give me the quickest diagnostic commands after a failed build"**
  - Primary: `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md`
  - Secondary: `54_CONFIGURE_LOG_SIGNAL_EXTRACTION_GUIDE.md`, `43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md`

- **"How can I make configure/build settings reproducible across local and CI?" / "Should I use CMake presets?"**
  - Primary: `58_CMAKE_PRESETS_ADOPTION_GUIDE.md`
  - Secondary: `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`, `55_KNOWN_GOOD_STARTER_CONFIGS.md`

- **"Preset run fails" / "works locally but CI preset fails"**
  - Primary: `59_CMAKE_PRESETS_FAILURE_PATTERNS.md`
  - Secondary: `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md`, `44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md`

- **"How do I use Trilinos from my own CMake app?"**
  - Primary: `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`

- **"Which package should I look at first?"**
  - Primary: `05_PACKAGE_ROUTING_AND_TIERS.md`
  - Secondary: `03_PACKAGE_CATALOG.md`

- **"Who owns this package?"**
  - Primary: `03_PACKAGE_CATALOG.md`

- **"How should I contribute or report vulnerabilities?"**
  - Primary: `08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md`

### Fallback order when query is broad
1. `10_GETTING_STARTED_ROUTE.md`
2. `02_SOURCE_OF_TRUTH_MAP.md`
3. `04_BUILD_INSTALL_PLAYBOOK.md`
4. `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`

### Retrieval priorities
- Prefer documents with direct procedure content for action requests.
- Prefer source-of-truth and conventions docs for policy/verification requests.
- Prefer package routing/catalog docs for ownership and subsystem navigation.

## Validation
- Verify each referenced file exists in `library/Trilinos/`.
- Verify routing targets are still aligned after adding or renaming docs.

## Provenance
- `library/Trilinos/00_INDEX.md`
- `library/Trilinos/01_DOCUMENTATION_CONVENTIONS.md`
- `library/Trilinos/02_SOURCE_OF_TRUTH_MAP.md`
- `library/Trilinos/03_PACKAGE_CATALOG.md`
- `library/Trilinos/04_BUILD_INSTALL_PLAYBOOK.md`
- `library/Trilinos/05_PACKAGE_ROUTING_AND_TIERS.md`
- `library/Trilinos/06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
- `library/Trilinos/07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md`
- `library/Trilinos/08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md`
- `library/Trilinos/09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`
- `library/Trilinos/10_GETTING_STARTED_ROUTE.md`
