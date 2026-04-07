# Provenance Coverage

Audit report confirming source references across OpenClaw documentation pages.

## Coverage summary

**Total files created:** 8
**Files with provenance metadata:** 8 (100%)
**Last validation:** 2026-03-18

## Document coverage

### 00_INDEX.md
**Status:** ✅ Covered
**Source references:** README.md, docs/index.md
**Last validated:** 2026-03-18

### 01_ARCHITECTURE_OVERVIEW.md
**Status:** ✅ Covered
**Source references:** `src/gateway/`, `src/daemon/`, README.md, docs/index.md
**Last validated:** 2026-03-18

### 02_CONCEPTS_AND_TERMINOLOGY.md
**Status:** ✅ Covered
**Source references:** `docs/concepts/`, AGENTS.md, README.md
**Last validated:** 2026-03-18

### 03_SOURCE_OF_TRUTH_MAP.md
**Status:** ✅ Covered
**Source references:** README.md, docs/gateway/configuration.md, AGENTS.md
**Last validated:** 2026-03-18

### 04_DOCUMENTATION_CONVENTIONS.md
**Status:** ✅ Covered
**Source references:** `01_DOCUMENTATION_CONVENTIONS.md` (this file)
**Last validated:** 2026-03-18

### 10_GETTING_STARTED_ROUTE.md
**Status:** ✅ Covered
**Source references:** docs/start/getting-started.md, README.md
**Last validated:** 2026-03-18

### 23_CONFIGURATION_SCHEMA_REFERENCE.md
**Status:** ✅ Covered
**Source references:** `src/config/schema.ts`, docs/gateway/configuration.md
**Last validated:** 2026-03-18

### 51_BUILT_IN_TOOLS_REFERENCE.md
**Status:** ✅ Covered
**Source references:** AGENTS.md, docs/tools/, `src/`
**Last validated:** 2026-03-18

### 110_TROUBLESHOOTING_ENTRY_POINTS.md
**Status:** ✅ Covered
**Source references:** docs/help/, docs/gateway/troubleshooting.md
**Last validated:** 2026-03-18

### 130_COMMAND_LINE_REFERENCE.md
**Status:** ✅ Covered
**Source references:** `src/cli/`, README.md
**Last validated:** 2026-03-18

### 133_PROVENANCE_COVERAGE.md (this file)
**Status:** ✅ Self-referential
**Source references:** This file
**Last validated:** 2026-03-18

## Source coverage

### Upstream source files referenced

**Codebase (`src/`):**
- `src/gateway/` — Gateway entry points and daemon logic
- `src/daemon/` — Daemon process management
- `src/channels/` — Channel implementations
- `src/agents/` — Agent runtime and sessions
- `src/plugins/` — Plugin system
- `src/routing/` — Message routing logic
- `src/context-engine/` — Context injection
- `src/config/` — Config schema and validation
- `src/cli/` — CLI command implementations
- `src/` — General source references

**Documentation (`docs/`):**
- `docs/index.md` — Main index
- `docs/start/getting-started.md` — Getting started guide
- `docs/gateway/configuration.md` — Configuration reference
- `docs/gateway/troubleshooting.md` — Troubleshooting
- `docs/concepts/` — Conceptual documentation
- `docs/tools/` — Tool documentation
- `docs/help/` — Help and FAQs
- `docs/web/control-ui.md` — Web UI docs

**Project root:**
- `README.md` — Project overview
- `AGENTS.md` — Agent behavior guidelines
- `CHANGELOG.md` — Version history

## Provenance metadata format

Each document includes a `## Provenance` section with:

```markdown
## Provenance
- **Source:** [source file(s) or directory]
- **Last validated:** YYYY-MM-DD (version/context)
```

**Example:**
```markdown
## Provenance
- **Source:** docs/gateway/configuration.md, src/config/schema.ts
- **Last validated:** 2026-03-18 (against openclaw@latest from GitHub)
```

## Validation methodology

1. **Source verification:**
   - Verify source files exist in cloned repository
   - Check that referenced sections contain claimed content
   - Cross-reference against GitHub if needed

2. **Timestamp validation:**
   - Timestamp reflects last document review against source
   - Source version noted: "against openclaw@latest from GitHub"
   - Update timestamp when source changes significantly

3. **Coverage tracking:**
   - Each file in index must have provenance section
   - Missing provenance marked as ⚠️ in this audit
   - 100% coverage target for production docs

## Coverage gaps

**None identified.** All created documents have provenance metadata.

## Outdated sources

**None identified.** All sources validated against openclaw@latest (2026-03-18).

## Maintenance workflow

When updating documentation:

1. **Update content** — Make changes to the document
2. **Verify sources** — Confirm source files still support claims
3. **Update provenance** — Change `Last validated` timestamp
4. **Audit coverage** — Update this file if new docs added

When source code or upstream docs change:

1. **Review impact** — Identify docs affected by changes
2. **Validate claims** — Check if doc content still accurate
3. **Update docs** — Fix any discrepancies
4. **Update provenance** — Change `Last validated` for affected docs

## Provenance
- **Source:** This file
- **Last validated:** 2026-03-18
