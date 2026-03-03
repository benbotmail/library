# Trilinos LLM Build/Install Prompt Templates

## Scope
Ready-to-use prompt templates for asking an LLM to produce reliable Trilinos build/install guidance with bounded assumptions and explicit provenance expectations.

## Why this page exists
Many weak answers come from underspecified prompts. These templates force critical context: toolchain mode, package scope, constraints, and required output format.

## Template 1 — First-success setup request
```text
You are helping with Trilinos setup.
Goal: produce a 30-minute first-success configure/build/install plan.
Constraints:
- Environment: <OS>, CMake <version>, compilers <details>
- MPI needed: <yes/no/unknown>
- Desired package scope: <minimal|selected|broad>
- Install prefix: <path>
Output requirements:
1) exact command sequence,
2) failure fallback for missing dependencies,
3) post-install verification checklist,
4) cite which Trilinos docs/pages each recommendation comes from.
```

## Template 2 — Configure failure triage request
```text
I have a Trilinos CMake configure failure.
Context:
- Build profile: <MPI/non-MPI, Debug/Release>
- Enabled packages: <list>
- TPL hints provided: <list>
- First decisive error block: <paste exact message>
Tasks:
1) classify likely root cause,
2) propose smallest next change,
3) provide one-change-at-a-time retry plan,
4) include a stop condition for escalating to broader diagnostics.
Use stage-specific triage logic, not compile-stage advice.
```

## Template 3 — Downstream integration request
```text
I installed Trilinos and need to build a downstream CMake app.
Provide:
1) minimal downstream cmake configure/build commands,
2) discovery strategy using Trilinos_DIR or CMAKE_PREFIX_PATH,
3) checks for MPI/compiler consistency,
4) runtime shared-library path guidance,
5) symptom router for find/link/runtime failures.
Keep the answer concise and command-first.
```

## Template 4 — CI acceleration request
```text
Design a Trilinos CI lane strategy with fast PR feedback and broader nightly coverage.
Inputs:
- Active packages: <list>
- Current CI pain point: <time/failures/flakiness>
Output:
1) lane A/B/C definitions,
2) what runs in each lane,
3) expected tradeoffs,
4) migration steps from current setup,
5) rollback criteria if signal quality drops.
```

## Prompt hygiene checklist
- Always specify MPI vs non-MPI intent.
- Provide package scope explicitly.
- Include first decisive error text for troubleshooting prompts.
- Require command-first output plus verification steps.
- Require provenance or explicit source references.

## Cross references
- `11_LLM_QUERY_ROUTING_HINTS.md`
- `26_BUILD_INSTALL_DECISION_TREE.md`
- `32_30_MINUTE_FIRST_SUCCESS_PATH.md`
- `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- `27_DOWNSTREAM_INTEGRATION_FAILURE_PATTERNS.md`

## Provenance
- Prompt patterns synthesized from recurrent guidance needs captured across local `library/Trilinos/` docs, especially build/install, triage, and routing pages.
