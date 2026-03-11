# Trilinos Cross-Reference Matrix

## Scope
Matrix linking common Trilinos tasks to the most relevant documents in this collection.

## Audience
- Engineers navigating docs quickly by task
- LLM systems selecting high-relevance retrieval targets

## Prerequisites
- Access to `library/Trilinos/00_INDEX.md`
- Basic understanding of Trilinos workflows (build, troubleshoot, integrate, contribute)

## Content

| Task | Primary Document | Secondary Document(s) |
|---|---|---|
| First install | `04_BUILD_INSTALL_PLAYBOOK.md` | `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`, `13_CMAKE_FLAG_QUICK_REFERENCE.md` |
| Dependency/TPL readiness | `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md` | `13_CMAKE_FLAG_QUICK_REFERENCE.md` |
| Pre-configure baseline setup (toolchain + MPI path + prefix) | `56_PLATFORM_BASELINE_AND_TOOLCHAIN_MINIMUMS.md` | `35_PRECONFIGURE_ENVIRONMENT_CHECKLIST.md`, `55_KNOWN_GOOD_STARTER_CONFIGS.md` |
| Configure-time failures | `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md` | `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`, `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md` |
| Fast post-failure diagnostic capture | `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md` | `54_CONFIGURE_LOG_SIGNAL_EXTRACTION_GUIDE.md`, `43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md` |
| Compile/link instability | `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md` | `17_PACKAGE_SELECTION_STRATEGY.md`, `04_BUILD_INSTALL_PLAYBOOK.md` |
| Downstream CMake integration | `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md` | `13_CMAKE_FLAG_QUICK_REFERENCE.md` |
| Reproducible local+CI configure workflows | `58_CMAKE_PRESETS_ADOPTION_GUIDE.md` | `59_CMAKE_PRESETS_FAILURE_PATTERNS.md`, `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`, `55_KNOWN_GOOD_STARTER_CONFIGS.md` |
| Preset migration from ad-hoc `cmake -D...` commands | `60_ADHOC_TO_PRESET_MIGRATION_CHECKLIST.md` | `58_CMAKE_PRESETS_ADOPTION_GUIDE.md`, `30_CMAKE_CONFIGURE_SCRIPT_TEMPLATE_LIBRARY.md`, `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md` |
| Environment/module/PATH contamination triage | `53_ENVIRONMENT_MODULES_AND_PATH_HYGIENE_GUIDE.md` | `56_PLATFORM_BASELINE_AND_TOOLCHAIN_MINIMUMS.md`, `35_PRECONFIGURE_ENVIRONMENT_CHECKLIST.md`, `46_COMPILER_AND_CXX_STANDARD_ALIGNMENT_GUIDE.md` |
| Package selection | `17_PACKAGE_SELECTION_STRATEGY.md` | `05_PACKAGE_ROUTING_AND_TIERS.md`, `03_PACKAGE_CATALOG.md` |
| Package ownership lookup | `03_PACKAGE_CATALOG.md` | `05_PACKAGE_ROUTING_AND_TIERS.md` |
| Source confidence verification | `02_SOURCE_OF_TRUTH_MAP.md` | `01_DOCUMENTATION_CONVENTIONS.md` |
| Contribution flow | `08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md` | `03_PACKAGE_CATALOG.md` |
| Security disclosure | `08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md` | `02_SOURCE_OF_TRUTH_MAP.md` |

### Routing notes
- Prefer primary document first for direct answers.
- Use secondary documents for constraints, variants, and validation details.
- If user intent is broad, start at `10_GETTING_STARTED_ROUTE.md`.

## Validation
- Re-validate matrix links whenever files are renamed or reordered.
- Ensure all matrix entries reference existing files listed in `00_INDEX.md`.

## Provenance
- `library/Trilinos/00_INDEX.md`
- `library/Trilinos/10_GETTING_STARTED_ROUTE.md`
- `library/Trilinos/11_LLM_QUERY_ROUTING_HINTS.md`
- `library/Trilinos/18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md`
