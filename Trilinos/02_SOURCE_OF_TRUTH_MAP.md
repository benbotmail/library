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

### Conflict resolution examples
| Scenario | Resolution |
|---|---|
| Wiki says enable `Foo` but `INSTALL.rst` says `Bar` | Prefer `INSTALL.rst` (level 2) over wiki (level 5) |
| `PackagesList.cmake` lists package dependency differently than a blog post | Prefer `PackagesList.cmake` (level 3) |
| Official docs portal and `README.md` disagree on CMake minimum | Prefer official docs portal (level 1); file an issue if discrepancy is real |
| Package-local `README` conflicts with top-level `INSTALL.rst` | Prefer top-level `INSTALL.rst` for global workflow; package docs for package-specific details |

### When to flag uncertainty
- Claim appears only in community/wiki sources (level 5) without level 1–3 corroboration
- Conflicting information exists at the same authority level
- Upstream documentation is outdated or missing for a known feature

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
