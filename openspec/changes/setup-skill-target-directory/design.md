## Context

The setup skill is a markdown-only skill (ADR-0008: extraction skills are markdown not code). It has `disable-model-invocation: true` in its frontmatter and all step instructions reference "the project root" without parameterization.

An orchestrator session in a different project needs to invoke this skill targeting a directory that is not its own project root. The skill needs to accept an optional target directory argument.

**In-force ADRs reviewed:**
- ADR-0008 (extraction skills are markdown not code): Still applies — this remains a markdown skill with no code.
- ADR-0009 (factory pattern via instruction branching): Relevant — adding a parameter that branches behavior (target dir vs. project root) follows this pattern.
- ADR-0012 (per-change manifest over global index): Not affected by this change.

## Goals / Non-Goals

**Goals:**
- Allow AI invocation of the setup skill (remove `disable-model-invocation`)
- Accept optional target directory argument
- When target is provided, all operations resolve paths relative to that directory
- Backward compatible: no argument = current behavior (project root)

**Non-Goals:**
- Adding a bin/ script or code-mode execution — this stays as a markdown skill
- Changing what the skill does (schema install, repair loop, INDEX cleanup all remain the same)
- Remote execution or cross-machine targeting

## Decisions

### 1. Argument passed as skill args string

**Decision:** The target directory is passed as the skill's args string (the text after the skill name when invoked).

**Why:** Claude Code skills already support an args parameter. No new mechanism needed. If empty/absent, default to project root (CWD).

### 2. Remove `disable-model-invocation` entirely

**Decision:** Remove the restriction rather than adding a conditional.

**Why:** The skill's operations (schema install, file deletion) are all safe idempotent operations. There's no security concern with AI invocation — and the orchestrator use case specifically requires it.

### 3. Path resolution: all "project root" references become target directory

**Decision:** Every step that references "the project root" resolves against the target directory when provided.

**Affected paths:**
- `CLAUDE.md` detection → `<target>/CLAUDE.md`
- `openspec schema which` → run from `<target>/` (or pass path if CLI supports it)
- Schema install → `<target>/openspec/schemas/`
- `adr/INDEX.md` → `<target>/adr/INDEX.md`

## Risks / Trade-offs

- **`openspec` CLI may not support `--project` flag** → If `openspec schema which` must run from the project directory, the skill instructions should note to `cd` to the target first. Mitigation: test during implementation; if needed, prefix commands with `cd <target> &&`.
- **Target directory might not exist** → Mitigation: add a step 0 that verifies the directory exists and is accessible before proceeding.
