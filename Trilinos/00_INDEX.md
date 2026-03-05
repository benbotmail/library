# Trilinos LLM Documentation Pack

This collection is a practical, retrieval-friendly guide to working with the Trilinos repository.

## Core orientation
- `01_DOCUMENTATION_CONVENTIONS.md` — Documentation rules for structure, claim grounding, and provenance.
- `02_SOURCE_OF_TRUTH_MAP.md` — Authority order for Trilinos sources when claims conflict.
- `10_GETTING_STARTED_ROUTE.md` — Task-based starting route from first install to downstream use.
- `16_REPO_SURFACE_MAP.md` — Top-level map of repository directories and where to look first.
- `12_TERMS_AND_METADATA_GLOSSARY.md` — Glossary for Trilinos, TriBITS, TPL, and metadata terms.

## Build and configuration
- `04_BUILD_INSTALL_PLAYBOOK.md` — Quick configure/build/install paths (MPI and non-MPI).
- `13_CMAKE_FLAG_QUICK_REFERENCE.md` — High-utility CMake flag reference.
- `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md` — Build profile templates from minimal to broad coverage.
- `40_BUILD_PROFILE_SELECTION_MATRIX.md` — At-a-glance goal-to-profile matrix for choosing safe starting build configurations.
- `26_BUILD_INSTALL_DECISION_TREE.md` — Fast strategy selector for MPI/non-MPI, package scope, and escalation paths.
- `28_CI_AND_LOCAL_BUILD_TIME_REDUCTION_GUIDE.md` — Practical lane and workflow tactics to reduce build/test turnaround.
- `29_INSTALL_VERIFICATION_CHECKLIST.md` — Post-install acceptance checklist for downstream readiness.
- `07_BUILDING_DOWNSTREAM_APPS_WITH_TRILINOS.md` — Guidance for configuring and building downstream CMake applications against installed Trilinos.
- `30_CMAKE_CONFIGURE_SCRIPT_TEMPLATE_LIBRARY.md` — Reusable `do-configure` templates for common build scenarios.
- `31_TPL_DISCOVERY_AND_PATH_HINTS.md` — Practical path-hint and triage guide for missing/partial TPL detection.
- `32_30_MINUTE_FIRST_SUCCESS_PATH.md` — Time-boxed first-success route from clean setup to downstream smoke validation.
- `33_BUILD_AND_INSTALL_COMMAND_CHEATSHEET.md` — Copy/paste command patterns for common configure/build/install scenarios.
- `34_BUILD_INSTALL_DOC_NAVIGATION_MAP.md` — Intent-to-document router for fast navigation across build/install docs.
- `41_RETRIEVAL_ENTRYPOINTS_BUILD_INSTALL.md` — Query-cue entrypoints for fast LLM retrieval on build/install intents.
- `42_BUILD_INSTALL_MINIMUM_CONTEXT_SCHEMA.md` — Minimal intake schema for accurate, low-assumption build/install guidance.
- `49_BUILD_INSTALL_INTAKE_QUESTIONNAIRE.md` — Compact first-turn question set and template for high-signal build/install triage intake.
- `50_FIRST_RESPONSE_TRIAGE_MACRO.md` — Deterministic first-turn response pattern for stage classification, minimal context request, and single-step routing.
- `51_SHARED_STATIC_LINKAGE_CONSISTENCY_GUIDE.md` — Practical guidance for preventing and triaging shared/static linkage mismatches across Trilinos, TPLs, and downstream apps.
- `52_RUNTIME_LOADER_RPATH_TRIAGE.md` — Deterministic runtime loader and RPATH troubleshooting flow for post-build startup failures.
- `53_ENVIRONMENT_MODULES_AND_PATH_HYGIENE_GUIDE.md` — Clean-room path/module hygiene workflow to prevent mixed toolchain and prefix-shadowing build failures.
- `54_CONFIGURE_LOG_SIGNAL_EXTRACTION_GUIDE.md` — Fast extraction of high-signal configure errors from large CMake logs, with deterministic next-step routing.
- `43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md` — Standardized log-capture bundle and minimal-repro template for faster troubleshooting handoffs.
- `35_PRECONFIGURE_ENVIRONMENT_CHECKLIST.md` — Preflight checks to reduce avoidable configure/build failures.
- `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md` — Step-by-step configure-stage failure isolation and recovery flow.
- `37_LLM_BUILD_INSTALL_PROMPT_TEMPLATES.md` — Reusable prompt templates for high-quality LLM build/install and triage outputs.
- `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md` — Dependency/TPL readiness and accelerator-related TPL signals.
- `17_PACKAGE_SELECTION_STRATEGY.md` — Framework for choosing initial package sets before expansion.

## Troubleshooting and routing
- `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md` — Configure/build failure matrix with fixes.
- `18_ERROR_PATTERN_ROUTER_BY_BUILD_STAGE.md` — Stage-based router for configure/build/test/integration failures.
- `11_LLM_QUERY_ROUTING_HINTS.md` — Query-intent routing map for LLM retrieval.
- `19_CROSS_REFERENCE_MATRIX.md` — Task-to-document matrix with primary and secondary references.
- `21_CANONICAL_WORKFLOWS_BY_USER_GOAL.md` — Goal-based workflow sequences for install, package selection, troubleshooting, integration, and contribution paths.
- `22_FREQUENT_BUILD_QUESTIONS.md` — FAQ-style quick answers for recurring Trilinos build and configuration decisions.
- `27_DOWNSTREAM_INTEGRATION_FAILURE_PATTERNS.md` — Symptom-to-fix router for downstream CMake integration and linking/runtime failures.
- `39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md` — Post-build install/runtime failure routing for loader, ABI, and MPI consistency issues.
- `44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md` — Focused checklist for MPI wrapper/compiler/launcher alignment and ABI-consistency triage.
- `45_BUILD_INSTALL_ESCALATION_HANDOFF_CHECKLIST.md` — Escalation packet checklist and template for unresolved build/install failures.
- `46_COMPILER_AND_CXX_STANDARD_ALIGNMENT_GUIDE.md` — Practical checklist for compiler-family and C++ standard alignment across configure/build/downstream integration.
- `47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md` — Standard reset-and-reconfigure workflow to eliminate stale-cache-induced configure/build failures.
- `48_TRIAGE_DECISION_ORDER_BUILD_INSTALL_ISSUES.md` — Ordered troubleshooting sequence to prioritize high-probability causes and improve escalation quality.
- `23_REFERENCE_LINKS_AND_ANCHORS.md` — Central link and anchor index for authoritative Trilinos web and repository references.
- `24_LINK_INTEGRITY_VALIDATION.md` — Validation report confirming internal local-link integrity across the Trilinos docs pack.
- `25_PROVENANCE_COVERAGE_AUDIT.md` — Audit report confirming provenance-section coverage across docs pages.
- `38_INDEX_COVERAGE_AUDIT.md` — Validation report confirming index completeness against local docs files.

## Package and ownership references
- `03_PACKAGE_CATALOG.md` — Package directory catalog with CODEOWNERS mapping where available.
- `05_PACKAGE_ROUTING_AND_TIERS.md` — Package-family routing and tier signal interpretation.

## Contribution and governance
- `08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md` — Contribution flow, DCO sign-off, and security disclosure path.
- `20_SUPPORT_AND_COMPATIBILITY_BOUNDARIES.md` — Support boundary guide for tested compiler/MPI expectations and security support scope.
- `15_DOC_QUALITY_CHECKLIST.md` — Quality gates for schema consistency, provenance, and retrieval readiness.

## Primary external references
- Upstream repo: <https://github.com/trilinos/Trilinos>
- Official docs index: <https://trilinos.github.io/documentation.html>
