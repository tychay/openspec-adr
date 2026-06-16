# OpenSpec ADR (spec-driven-with-adr)

Portable rules and schema for integrating Architecture Decision Records into the
OpenSpec spec-driven workflow. Include this as a submodule in any project that
uses OpenSpec and wants durable architectural decision capture.

## What's Here

```
ai/openspec-adr/
├── README.md              ← you are here (install + bootstrap instructions)
├── CLAUDE-RULES.md        ← portable ADR operational rules for Claude
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
vault notes, archived changes, etc.:

1. Identify decisions using the heuristic in CLAUDE-RULES.md (constrains future
   work, has alternatives, outlives the change, non-obvious)
2. Create ADR files in `adr/` with sequential numbering
3. Update `adr/INDEX.md` with the new entries
4. A bootstrapping skill for automated extraction is planned for a future change

## Philosophy

This uses `spec-driven-with-adr` (not `intent-driven`):
- ADR step is additive, not a gate — skippable when no architectural consequence
- "adr-last" approach: it's okay to capture decisions after implementation
- Fallback: projects without this schema use `spec-driven` and note decisions in
  design.md for later extraction
