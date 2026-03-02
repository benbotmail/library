# Trilinos CI and Local Build-Time Reduction Guide

## Scope
Practical tactics to reduce configure/build/test turnaround time for Trilinos workflows in local development and CI.

## Goals
- Shorten iteration loops without losing key validation signal
- Separate fast feedback paths from broad/nightly coverage

## Fast-feedback strategy (local + PR)
1. **Start with targeted package enables**
   - Prefer `Trilinos_ENABLE_<Package>=ON` for active work area.
   - Avoid broad package enables during tight edit-compile-debug loops.

2. **Use out-of-source, profile-specific build dirs**
   - Keep separate build trees for Debug, Release, MPI, and non-MPI to avoid expensive reconfigure churn.

3. **Pick a fast generator and parallel build**
   - Use Ninja where available.
   - Set parallelism to host capacity (`-j<n>` / Ninja default workers tuned as needed).

4. **Defer expensive test sets while iterating**
   - Run focused tests tied to changed packages first.
   - Reserve full regression suites for later gates/nightlies.

5. **Cache configuration intent**
   - Keep `do-configure` scripts for each profile to reduce option drift and rework.

## CI lane design pattern
- **Lane A (PR quick gate):** minimal required packages + key smoke tests
- **Lane B (PR extended):** broader package set + integration tests
- **Lane C (nightly/full):** wide package/TPL coverage + heavier validations

This layered approach keeps PR cycle time reasonable while preserving high-confidence coverage over time.

## Common time sinks and mitigations

### Repeated full reconfigure after small option changes
- **Mitigation:** maintain stable configure scripts per profile; avoid unnecessary toggling in a single build tree.

### Overly broad package scope for narrow code changes
- **Mitigation:** route by package ownership and dependency impact; enable only what changed plus near-neighbors.

### Running full tests for every local iteration
- **Mitigation:** adopt staged tests (smoke → focused → broader).

### Toolchain mismatches causing rebuild/retry loops
- **Mitigation:** pin compiler/MPI combinations per profile and document them alongside configure scripts.

## Minimal checklist before broadening scope
- Build completes for active package set
- Focused tests pass
- Downstream integration smoke check passes (if affected)
- Then expand to broader package/test lanes

## Cross references
- `04_BUILD_INSTALL_PLAYBOOK.md`
- `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`
- `17_PACKAGE_SELECTION_STRATEGY.md`
- `21_CANONICAL_WORKFLOWS_BY_USER_GOAL.md`
- `22_FREQUENT_BUILD_QUESTIONS.md`

## Provenance
- `Trilinos/README.md`
- `Trilinos/INSTALL.rst`
- Trilinos CI/workflow practices as reflected in repository contribution/testing documentation and package-scoped build guidance
