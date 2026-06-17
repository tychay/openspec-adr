# OpenSpec ADR (spec-driven-with-adr)

Portable rules and schema for integrating Architecture Decision Records into the
OpenSpec spec-driven workflow. Include this as a submodule in any project that
uses OpenSpec and wants durable architectural decision capture.

## What's Here

```
ai/openspec-adr/
├── README.md              ← you are here (install + bootstrap instructions)
├── CLAUDE-RULES.md        ← portable ADR operational rules for Claude
├── .claude/skills/
│   ├── evaluate-file-for-adrs.md  ← subskill: extract ADR candidates from a doc
│   ├── add-adrs-from-file.md      ← skill: evaluate + approve + write (single source)
│   └── bootstrap-adrs.md          ← workflow: initial population from multiple sources
└── schemas/
    └── spec-driven-with-adr/  ← schema files (schema.yaml + templates)
```

## Install

### 1. Add this submodule to your project

```bash
git submodule add <remote-url> ai/openspec-adr
```

### 2. Copy the schema into your project's openspec

```bash
cp -r ai/openspec-adr/schemas/spec-driven-with-adr openspec/schemas/
```

### 3. Update openspec config

In `openspec/config.yaml`:
```yaml
schema: spec-driven-with-adr
```

### 4. Create `adr/` directories

At your project root and each submodule that has its own `openspec/`:
```bash
mkdir -p adr
```

Each `adr/` directory needs an `INDEX.md` listing in-force ADRs:
```markdown
# Active ADRs

Currently-in-force architectural decisions for this scope.

<!-- Format: - [NNNN-title](NNNN-title.md) — one-line summary -->
```

### 5. Reference rules from CLAUDE.md

Add to your project's CLAUDE.md (in the OpenSpec or workflow section):
```markdown
- **ADR Workflow:** See `ai/openspec-adr/CLAUDE-RULES.md` for ADR operational rules.
```

### 6. Validate

```bash
openspec schema validate
```

## Bootstrapping ADRs from Existing Documentation

If your project already has architectural decisions scattered across design docs,
vault notes, archived changes, etc., use the extraction skills:

### Quick start (bootstrap)

Run the `bootstrap-adrs` skill targeting your project:
1. It asks which documents to evaluate
2. It extracts candidates using the 4-point heuristic
3. It deduplicates and prunes inactive decisions
4. You approve/reject in markdown
5. It writes ADR files and updates INDEX.md

### Ad hoc (ongoing)

After bootstrap, use `add-adrs-from-file` when you encounter a new document
with architectural decisions worth capturing.

### Manual (fallback)

Without the skills:
1. Identify decisions using the heuristic in CLAUDE-RULES.md (constrains future
   work, has alternatives, outlives the change, non-obvious)
2. Create ADR files in `adr/` with sequential numbering
3. Update `adr/INDEX.md` with the new entries

## Philosophy

This uses `spec-driven-with-adr` (not `intent-driven`):
- ADR step is additive, not a gate — skippable when no architectural consequence
- "adr-last" approach: it's okay to capture decisions after implementation
- Fallback: projects without this schema use `spec-driven` and note decisions in
  design.md for later extraction
