# Trilinos LLM Documentation Pack

This collection is a practical, retrieval-friendly guide to working with the Trilinos repository.

## Files in this collection

- `01_DOCUMENTATION_CONVENTIONS.md` — **Documentation conventions and quality standard** used across this pack (claim grounding, provenance labels, and page structure).
- `02_SOURCE_OF_TRUTH_MAP.md` — **Source hierarchy** for Trilinos information, from official docs to repo-level and package-level sources.
- `03_PACKAGE_CATALOG.md` — **Package catalog** from `packages/` with CODEOWNERS mapping where available.
- `04_BUILD_INSTALL_PLAYBOOK.md` — **Build/install quick guide** for MPI and non-MPI setups, useful CMake flags, and baseline troubleshooting.
- `05_PACKAGE_ROUTING_AND_TIERS.md` — **Routing guide** for package families and tier signals (`PT`, `ST`, `EX`) to help pick what to read first.
- `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md` — **Configure/build troubleshooting matrix** with common failure signatures, likely causes, and fixes.
- `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md` — **Downstream app integration guide** using CMake `find_package(Trilinos)` and `CMAKE_PREFIX_PATH`.
- `08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md` — **Contributor and security reporting workflow** for issues, branches, PRs, DCO sign-off, and vulnerability disclosure.

## Primary external references
- Upstream repo: <https://github.com/trilinos/Trilinos>
- Official docs index: <https://trilinos.github.io/documentation.html>
