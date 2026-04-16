# Trilinos Support and Compatibility Boundaries

## Scope
Documented support boundaries for compiler/MPI coverage and security update scope.

## Audience
- Engineers deciding whether a target toolchain/platform is within documented support expectations
- LLM systems answering support-policy and risk questions

## Prerequisites
- Access to current Trilinos release and build documentation
- Awareness of intended compiler/MPI/toolchain target

## Content

### Compiler and MPI support expectations
Trilinos documents tested compiler/MPI combinations through its pull-request testing interface. Configurations outside automated testing are not guaranteed to work.

Practical implication:
- If your compiler/MPI pair is not part of tested combinations, expect additional integration risk.
- Upstream may still accept improvements via pull requests for broader compatibility.

### Security support scope
For security updates, Trilinos documents support for the latest released version.

Practical implication:
- Security fixes should be validated against the latest release lineage.
- Older deployments may require upgrade planning to remain within supported security scope.

### Security disclosure boundary
- Ordinary defects can be reported through public GitHub issues.
- Security vulnerabilities should be reported through GitHub Security reporting, not public issues.

### Risk-aware deployment guidance
1. Choose a tested compiler/MPI combination when possible.
2. Validate build profiles and TPL readiness before broad package enables.
3. Track release updates for security posture.
4. Use private disclosure path for vulnerabilities.

### Quick compatibility check
Before starting a build, verify your toolchain against tested combinations:

| Check | Where to verify |
|---|---|
| Tested compiler/MPI pairs | GitHub PR testing interface wiki |
| CMake minimum version | `INSTALL.rst` and top-level `CMakeLists.txt` |
| TPL version expectations | `TPLsList.cmake` and package-specific docs |
| Current release version | GitHub Releases page |

### Common out-of-scope scenarios
These situations require extra effort and may not be supported by upstream:
- Compilers not in PR testing matrix (may work but no guarantees)
- Older MPI implementations with known ABI incompatibilities
- Experimental accelerator backends without CI coverage
- End-of-life OS distributions with outdated system libraries

### See also
- `56_PLATFORM_BASELINE_AND_TOOLCHAIN_MINIMUMS.md` — Pre-configure toolchain baseline and minimums
- `46_COMPILER_AND_CXX_STANDARD_ALIGNMENT_GUIDE.md` — Compiler and C++ standard alignment
- `08_CONTRIBUTING_AND_SECURITY_WORKFLOW.md` — Contribution flow and security disclosure path
- `09_TPL_BASELINE_AND_ACCELERATOR_SIGNALS.md` — TPL baseline expectations and version signals

## Validation
- Verify tested compiler/MPI references against current project testing interface documentation.
- Verify security support statements against the current `SECURITY.md` and release page.
- Cross-references validated against existing docs pack (2026-04-13).

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/SECURITY.md`
- `Trilinos/README.md`
- Pull-request testing interface: <https://github.com/trilinos/Trilinos/wiki/Pull-Request-Testing-Interface>
- Releases: <https://github.com/trilinos/Trilinos/releases>
