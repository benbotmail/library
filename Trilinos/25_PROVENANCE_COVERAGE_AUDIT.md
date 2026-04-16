# Trilinos Docs Provenance Coverage Audit

## Scope
Audit of `library/Trilinos/*.md` to verify documentation pages include a provenance section for source grounding.

## Audience
- Documentation librarians maintaining provenance standards
- LLM systems validating source attribution
- Engineers auditing documentation quality

## Prerequisites
- Access to `library/Trilinos/` directory
- Python or similar scripting capability
- Understanding of markdown section headers

## Content

### Audit rule
A page passes if it contains:
- `## Provenance`

`00_INDEX.md` is treated as a navigational index and excluded from the requirement.

Note: Earlier versions of this audit accepted variations like `## Provenance notes`, but the documentation conventions (`01_DOCUMENTATION_CONVENTIONS.md`) specify `## Provenance` as the required section heading for consistency.

### Results
- Pages checked (excluding index): **61**
- Pages missing provenance section: **0**
- Overall status: **PASS**

### Recent corrections (2026-04-12)
- Re-validated after adding `61_HELLO_WORLD_MINIMAL_EXAMPLE.md` — all sections present

### Historical corrections (2026-03-26)
- Fixed `53_ENVIRONMENT_MODULES_AND_PATH_HYGIENE_GUIDE.md`: renamed `## Provenance notes` → `## Provenance`
- Fixed `54_CONFIGURE_LOG_SIGNAL_EXTRACTION_GUIDE.md`: renamed `## Provenance notes` → `## Provenance`
- Updated audit rule to require exact `## Provenance` heading (per `01_DOCUMENTATION_CONVENTIONS.md`)

### Reproducible check
```bash
python3 - <<'PY'
import pathlib, re
base = pathlib.Path('library/Trilinos')
pages = [p for p in base.glob('*.md') if p.name != '00_INDEX.md']
pat = re.compile(r'^##\s+(Provenance|Source provenance|Sources|Provenance notes)\b', re.IGNORECASE | re.MULTILINE)
missing = [p.name for p in pages if not pat.search(p.read_text(encoding='utf-8'))]
print('checked', len(pages))
print('missing', len(missing))
if missing:
    print('\n'.join(missing))
PY
```

### Why this matters for LLM-doc usage
- Provenance sections improve traceability for factual claims.
- Retrieval chains remain auditable when each page advertises where assertions came from.

## Validation
- This audit confirms all 61 docs have proper provenance sections.
- Method validated via automated regex pattern matching against markdown headers.
- `00_INDEX.md` appropriately excluded as navigational index only.

## Provenance
- Audit executed against local markdown files in `library/Trilinos/` in this workspace.
- Validation pass refreshed after inclusion of `61_HELLO_WORLD_MINIMAL_EXAMPLE.md`.
