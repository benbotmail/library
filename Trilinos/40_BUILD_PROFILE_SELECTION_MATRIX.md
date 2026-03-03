# Trilinos Build Profile Selection Matrix

## Scope
At-a-glance matrix for selecting an initial Trilinos build profile based on goal, environment maturity, and risk tolerance.

## How to use
1. Find your primary goal row.
2. Start with the recommended profile.
3. Apply the listed guardrails before expanding scope.

## Selection matrix

| Goal | Recommended first profile | Why this is the default | Guardrails before broadening |
|---|---|---|---|
| First successful install on new machine | Non-MPI + minimal packages + Debug | Fastest path to isolate environment issues | Confirm install metadata exists and downstream smoke build passes |
| HPC app integration (MPI required) | MPI + selected packages + RelWithDebInfo | Balances runtime realism with diagnosability | Keep MPI wrappers consistent and validate required package set only |
| Broad capability validation | MPI + broad packages + Release | Maximizes feature coverage once baseline is stable | Only start after TPL readiness and minimal profile success |
| Fast local iteration while developing one area | Non-MPI or MPI (matching target) + targeted packages + Debug | Minimizes rebuild cost and failure surface | Avoid unrelated package enables; run focused tests first |
| CI quick PR gate | Minimal/selected packages + smoke tests | Preserves fast feedback cycle | Defer broad package/test lanes to nightly/extended CI |

## Risk signals (choose narrower profile when present)
- Unknown compiler/MPI compatibility on this host
- TPL install locations are incomplete or uncertain
- Frequent configure-stage failures in recent attempts
- Need rapid debug loop over broad feature validation

## Expansion order (recommended)
1. Minimal or selected package profile succeeds.
2. Install verification + downstream smoke check succeed.
3. Add packages incrementally.
4. Enable broader tests/coverage.
5. Move to broad validation profile.

## Cross references
- `14_BUILD_PROFILES_MINIMAL_TO_ADVANCED.md`
- `26_BUILD_INSTALL_DECISION_TREE.md`
- `32_30_MINUTE_FIRST_SUCCESS_PATH.md`
- `28_CI_AND_LOCAL_BUILD_TIME_REDUCTION_GUIDE.md`
- `29_INSTALL_VERIFICATION_CHECKLIST.md`

## Provenance
- `Trilinos/INSTALL.rst`
- `Trilinos/README.md`
- Profile and escalation guidance synthesized from local `library/Trilinos/` build/install docs
