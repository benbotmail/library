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
| Configure-time failures | `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md` | `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md` |
| Compile/link instability | `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md` | `17_PACKAGE_SELECTION_STRATEGY.md`, `04_BUILD_INSTALL_PLAYBOOK.md` |
| Downstream CMake integration | `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md` | `13_CMAKE_FLAG_QUICK_REFERENCE.md` |
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
