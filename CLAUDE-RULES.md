# ADR Workflow Rules

Operational rules for Architecture Decision Records in the spec-driven-with-adr
workflow. These rules are portable — include via reference from any project's
CLAUDE.md.

## When to Produce an ADR

During the ADR step of a change (after design.md, before tasks.md), evaluate
whether the design introduces decisions worth recording. Apply this heuristic:

A decision qualifies as an ADR when ALL of these are true:
1. **Constrains future work** — if someone doesn't know this, they'll make the
   wrong choice later
2. **Has rejected alternatives** — there was a real choice between viable options
3. **Outlives the change** — the constraint persists across many future changes
4. **Is non-obvious** — someone reading the code would ask "why is it this way?"

If none of these apply → skip the ADR step entirely. No stub files.

## When to Skip

- Pure implementation tasks (no structural choices)
- Changes with fewer than 10 lines
- Bug fixes that restore intended behavior
- Content-only edits

When skipping, the ADR artifact step still "completes" — it just produces nothing.

## Format

Each ADR file follows this structure:
```markdown
# NNNN. <Decision title>

- Status: proposed | accepted | accepted, supersedes ADR-NNNN
- Date: YYYY-MM-DD
<!-- Supersedes: ADR-NNNN  (only if replacing a prior ADR) -->

## Context
<!-- Forces at play, what prompted this -->

## Decision
<!-- The choice, stated clearly -->

## Consequences
<!-- What becomes easier? What becomes harder? -->
```

## Naming and Location

- Files live in `adr/` at the root of the project or submodule (NOT inside
  `openspec/`)
- Filename: `NNNN-kebab-title.md` (zero-padded 4 digits, e.g., `0001-park-not-delete.md`)
- Sequence is monotonic — never reuse a number, even for superseded ADRs

## Immutability Rule

**Once an ADR has status `accepted`, its file MUST NOT be edited.** Ever. Not the
status, not the body, not the date.

To change a decision:
1. Write a NEW ADR with status `accepted, supersedes ADR-NNNN`
2. Include `<!-- Supersedes: ADR-NNNN -->` in the new file
3. Explain in Context why the prior decision is being revisited
4. Remove the old entry from `adr/INDEX.md`
5. Add the new entry to `adr/INDEX.md`

The old file remains on disk unchanged as historical record.

## INDEX.md — The Active Manifest

Each `adr/` directory contains an `INDEX.md` listing currently-in-force ADRs.
This is what Claude reads to know active constraints.

- When starting a design: read `adr/INDEX.md` for the relevant scope
- New design choices MUST be coherent with in-force ADRs
- If a design needs to contradict an in-force ADR, flag it in Open Questions —
  the ADR step will produce the superseding record

Format:
```markdown
# Active ADRs

- [0001-park-not-delete](0001-park-not-delete.md) — Tasks removed from sync are parked, not deleted
- [0003-tiered-mutation](0003-tiered-mutation.md) — osascript for ≤10 ops, URL scheme for batch
```

## Schema Preference and Fallback

- If `openspec/config.yaml` has `schema: spec-driven-with-adr` → follow the full
  ADR-inclusive flow
- If config has `schema: spec-driven` (or schema not installed) → use standard
  flow, note architectural decisions in design.md for later extraction
- When initializing openspec in a new project: set `spec-driven-with-adr` if this
  submodule (`ai/openspec-adr/`) is present

## Companion Rule: adr/ Alongside openspec/

When creating a new `openspec/` directory (for a submodule, new project, etc.),
also create a sibling `adr/` directory with an empty `INDEX.md`.
