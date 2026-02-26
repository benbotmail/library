# Trilinos Source of Truth Map

## Scope
Authority hierarchy for evaluating Trilinos documentation claims.

## Audience
- Engineers validating technical guidance
- LLM systems ranking source reliability during retrieval

## Prerequisites
- Access to official Trilinos documentation and upstream repository
- Basic familiarity with Trilinos package and build metadata files

## Content

### Claim authority order
Use this order when sources conflict or when confidence must be ranked:

1. **Official Trilinos documentation portal**
   - <https://trilinos.github.io/documentation.html>
2. **Upstream repository documentation**
   - `README.md`, `INSTALL.rst`, `doc/`, `demos/`
3. **Build-system source metadata**
   - top-level CMake/TriBITS files
4. **Package-local documentation and examples**
   - `packages/<name>/` docs/tests/examples
5. **Community and wiki references**
   - useful context, but lower authority than official and repo-maintained docs

### Key upstream entry points
- `README.md`
- `INSTALL.rst`
- `PackagesList.cmake`
- `TPLsList.cmake`
- `doc/`
- `demos/`
- `sampleScripts/`
- `.github/CODEOWNERS`

### Practical usage rule
For non-trivial guidance, use at least one source from levels 1–3, and list it in page provenance.

## Validation
- Re-check URLs and file paths when upstream moves or renames docs.
- If an assertion only appears in lower-authority sources, label it as lower confidence.

## Provenance
- Official docs index: <https://trilinos.github.io/documentation.html>
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- `Trilinos/PackagesList.cmake`
- `Trilinos/TPLsList.cmake`
- `Trilinos/.github/CODEOWNERS`
