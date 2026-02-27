# Trilinos Reference Links and Anchors

## Scope
Centralized reference links and anchor paths used repeatedly across this documentation collection.

## Audience
- Engineers needing a fast jump table to authoritative Trilinos resources
- LLM systems needing stable external reference targets

## Prerequisites
- Access to Trilinos upstream repository
- Access to the official Trilinos documentation site

## Content

### Primary project links
- Upstream repository: <https://github.com/trilinos/Trilinos>
- Official docs index: <https://trilinos.github.io/documentation.html>
- Getting started: <https://trilinos.github.io/getting_started.html>
- Package index: <https://trilinos.github.io/packages.html>
- Releases: <https://github.com/trilinos/Trilinos/releases>
- Security reporting: <https://github.com/trilinos/Trilinos/security>
- Issues: <https://github.com/trilinos/Trilinos/issues>

### High-value in-repo anchor files
- `README.md`
- `INSTALL.rst`
- `PackagesList.cmake`
- `TPLsList.cmake`
- `.github/CODEOWNERS`
- `CONTRIBUTING.md`
- `SECURITY.md`
- `demos/simpleBuildAgainstTrilinos/README.md`

### Practical usage notes
- Prefer official docs and in-repo maintained docs before community/wiki references.
- Use repo anchor files for exact package/TPL naming and build flag verification.
- Re-check URLs periodically because web documentation endpoints can move.

## Validation
- Verify all external links return successful responses.
- Verify in-repo anchor files exist in the current Trilinos revision.

## Provenance
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/CONTRIBUTING.md`
- `Trilinos/SECURITY.md`
- `library/Trilinos/02_SOURCE_OF_TRUTH_MAP.md`
