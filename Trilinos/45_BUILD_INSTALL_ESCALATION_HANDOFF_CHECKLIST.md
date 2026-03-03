# Build/Install Escalation Handoff Checklist

## Scope
Define a concise, high-signal handoff format for escalating Trilinos build/install issues after local triage and minimal-repro reduction.

## Audience
- Engineers escalating unresolved Trilinos build/install failures
- Maintainers/reviewers receiving troubleshooting handoffs
- LLM assistants preparing escalation-ready summaries

## Prerequisites
- One completed local triage pass
- Captured logs and CMake cache artifacts
- A narrowed or minimal reproducer attempt

## Content

### When to escalate
Escalate when one or more are true:
- Failure persists after clean rebuild + reduced configuration.
- Error indicates probable toolchain/MPI/TPL incompatibility beyond local certainty.
- Local and CI-like runs diverge under nominally identical settings.
- Runtime/install failures persist after loader-path and ABI consistency checks.

### Handoff packet (required)
1. **Failure classification**
   - Stage: `configure`, `build`, `install`, or `runtime`
   - First decisive error line (exact text)
2. **Environment fingerprint**
   - OS/kernel
   - CMake version
   - Compiler versions
   - MPI implementation/wrapper identity (if relevant)
3. **Reproducer definition**
   - Exact configure command (or `do-configure` script)
   - Minimal flag set that still fails
   - Last known working variant (if known)
4. **Artifacts bundle**
   - `configure.log`, `build.log`, `install.log` (as applicable)
   - `CMakeCache.txt`
   - `CMakeFiles/CMakeOutput.log` and `CMakeFiles/CMakeError.log`
5. **What was already tried**
   - Clean build dir reset
   - Feature/TPL reduction sequence
   - Wrapper/compiler/path consistency checks

### Triage quality gates before sending
- Commands are copy/paste reproducible.
- Version and path details are explicit (no “latest/default” ambiguity).
- Error excerpt includes surrounding context (not only a truncated final line).
- Claimed “minimal repro” is actually smaller than initial failing config.

### Escalation template (copy/paste)
```text
Title: [Trilinos] <stage> failure: <short symptom>

Stage:
First decisive error:

Environment:
  OS/kernel:
  CMake:
  Compiler set:
  MPI stack/wrappers:

Reproducer:
  Configure command/script:
  Minimal failing options:
  Last known working options:

Artifacts:
  - configure.log
  - build.log / install.log
  - CMakeCache.txt
  - CMakeOutput.log / CMakeError.log

Attempts completed:
  - Clean rebuild: yes/no
  - Reduction pass: yes/no
  - MPI/ABI/path consistency checks: yes/no

Notes:
  Any CI/local differences, package scope notes, or likely boundary conditions.
```

## Validation
- Checklist includes both technical packet requirements and pre-send quality gates.
- Template aligns with stage-based troubleshooting and minimal-repro workflows.

## Provenance
- `library/Trilinos/36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- `library/Trilinos/39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`
- `library/Trilinos/43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md`
- `library/Trilinos/44_MPI_WRAPPER_AND_ABI_CONSISTENCY_CHECKLIST.md`
- Trilinos upstream repository docs: <https://github.com/trilinos/Trilinos>
- Official Trilinos documentation index: <https://trilinos.github.io/documentation.html>
