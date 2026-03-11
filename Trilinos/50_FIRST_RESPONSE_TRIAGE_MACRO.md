# First-Response Triage Macro (Build/Install)

## Scope
Provide a deterministic first-response macro for Trilinos build/install support so initial replies are fast, complete, and aligned with the docs pack.

## Audience
- Engineers answering Trilinos build/install questions
- LLM assistants generating first-turn troubleshooting responses

## Prerequisites
- User has provided at least partial failure context
- Access to this docs pack for routing

## Content

### First-response goals
1. Classify the failure stage.
2. Request any missing minimum context.
3. Give one safe, high-probability next action.
4. Route to exactly one primary troubleshooting page.

### Macro template
```text
Thanks — I can help. Based on what you shared, this looks like a <stage> issue.

Before deeper diagnosis, please provide:
- <missing context items only>

Next safe step:
- <single concrete action>

If this fails again, open:
- <primary page>
```

### Stage-to-primary-page mapping
- `configure` → `36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- `build` → `06_TROUBLESHOOTING_MATRIX_CONFIGURE_BUILD.md`
- `install`/`runtime` → `39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`
- `preset-specific` (local/CI divergence, preset not found, preset drift) → `59_CMAKE_PRESETS_FAILURE_PATTERNS.md`
- unknown/ambiguous stage → `57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md` (capture first, then route)

### Missing-context defaults
If key context is absent, request only from:
- `49_BUILD_INSTALL_INTAKE_QUESTIONNAIRE.md`
- `42_BUILD_INSTALL_MINIMUM_CONTEXT_SCHEMA.md`

Preset-aware minimum add-on (request only when presets are mentioned):
- configure/build preset name(s)
- whether `CMakeUserPresets.json` is present
- whether CI injects extra `-D` overrides beyond project presets

### Guardrails
- Avoid giving multiple speculative fixes in first response.
- Prefer one reversible step over broad environment changes.
- If stage is unclear, prefer diagnostic capture commands before prescribing fixes (`57_BUILD_FAILURE_FASTPATH_COMMAND_BUNDLE.md`).
- Require clean-build reset when toolchain/MPI/TPL changed recently.

## Validation
- Macro enforces stage-first routing.
- Context requests remain minimal and standardized.
- First action is concrete and low risk.

## Provenance
- `library/Trilinos/36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- `library/Trilinos/39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`
- `library/Trilinos/48_TRIAGE_DECISION_ORDER_BUILD_INSTALL_ISSUES.md`
- `library/Trilinos/49_BUILD_INSTALL_INTAKE_QUESTIONNAIRE.md`
- `library/Trilinos/42_BUILD_INSTALL_MINIMUM_CONTEXT_SCHEMA.md`
