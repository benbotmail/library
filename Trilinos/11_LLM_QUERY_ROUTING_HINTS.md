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
  - Secondary: `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`

- **"Why does configure fail with missing dependencies?"**
  - Primary: `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
  - Secondary: `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md`

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
