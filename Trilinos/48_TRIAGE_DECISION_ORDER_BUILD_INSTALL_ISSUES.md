# Triage Decision Order for Build/Install Issues

## Scope
Define a strict decision order for diagnosing Trilinos build/install problems so troubleshooting starts with high-probability root causes and avoids low-value churn.

## Audience
- Engineers triaging Trilinos failures under time pressure
- LLM assistants producing deterministic troubleshooting guidance

## Prerequisites
- Access to failing configure/build/install output
- Ability to rerun configure/build from a clean directory

## Content

### Decision order (apply in sequence)
1. **Environment and toolchain sanity**
   - Confirm compiler, CMake, and MPI wrapper identity.
2. **Cache freshness**
   - Reset stale build directory when major settings changed.
3. **Package/profile scope**
   - Reduce to minimal package/profile to isolate failure domain.
4. **TPL and path resolution**
   - Verify dependency discovery and explicit path hints.
5. **Stage-specific failure routing**
   - Classify failure as configure/build/install/runtime and apply the matching router.
6. **Escalation readiness**
   - If unresolved, produce minimal repro + escalation handoff packet.

### Why this order works
- Front-loads checks that invalidate many downstream hypotheses.
- Prevents spending time on package-level speculation before environment/cache integrity is known.
- Improves reproducibility and handoff quality.

### Minimal triage checklist
- [ ] Compiler/MPI wrappers verified
- [ ] Clean configure attempted
- [ ] Minimal profile attempted
- [ ] TPL hints/path checks completed
- [ ] Error mapped to stage router
- [ ] Repro + logs prepared for escalation (if needed)

### Quick handoff line format
```text
Stage=<...>; Toolchain=<...>; CacheReset=<yes/no>; MinimalProfile=<...>; TPLCheck=<pass/fail>; NextAction=<...>
```

## Validation
- Decision order is consistent with existing playbooks and routers.
- Checklist is concise enough for repeated heartbeat-driven execution.

## Provenance
- `library/Trilinos/26_BUILD_INSTALL_DECISION_TREE.md`
- `library/Trilinos/36_CONFIGURE_FAILURE_TRIAGE_PLAYBOOK.md`
- `library/Trilinos/39_INSTALL_AND_RUNTIME_FAILURE_ROUTER.md`
- `library/Trilinos/43_BUILD_INSTALL_LOG_CAPTURE_AND_MIN_REPRO_TEMPLATE.md`
- `library/Trilinos/45_BUILD_INSTALL_ESCALATION_HANDOFF_CHECKLIST.md`
- `library/Trilinos/47_CMAKE_CACHE_RESET_AND_RECONFIGURE_PROTOCOL.md`
- Official Trilinos documentation index: <https://trilinos.github.io/documentation.html>
