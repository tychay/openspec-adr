# evaluate-file-for-adrs

Evaluate a source file (or folder) for ADR candidates. Produces structured
candidate list — does NOT write files.

---

## Inputs

1. **Source path**: A file or folder to evaluate for architectural decisions
2. **Target adr/ path**: The `adr/` directory these candidates would be written to (used for dedup against existing ADRs)

---

## Evaluation Flow

### Step 1: Detect source type and select evaluator

Check the source:

- **If the source is a directory containing `proposal.md` or `.openspec.yaml`** → use the **OpenSpec evaluator** (Section A below)
- **Otherwise** → use the **Default evaluator** (Section B below)

### Step 2: Read the source

- If file: read it fully
- If folder: read all `.md` files in the folder

### Step 3: Apply the ADR identification heuristic

For each passage in the source, check if it describes a decision that meets ALL FOUR criteria:

1. **Constrains future work** — if someone (or an AI) doesn't know this decision, they'll make the wrong choice later
2. **Has rejected alternatives** — there was a real choice between viable options ("we do X, not Y")
3. **Outlives the change** — the constraint persists across many future changes, not just this one
4. **Is non-obvious** — someone reading the code would ask "why is it this way?"

If a passage meets all four → produce a candidate. If only some → produce a candidate with lower confidence.

Implementation detail is NOT an ADR. Requirements are NOT ADRs. Temporary choices are NOT ADRs.

### Step 4: Filter against existing ADRs

Read all files in the target `adr/` directory. For each candidate:
- If an existing ADR's Decision section matches the candidate's decision in meaning → mark as duplicate and exclude
- If the target directory is empty (bootstrap case) → skip this step

### Step 5: Produce output

Return a structured list of candidates (see Output Schema below).

---

## Section A: OpenSpec Evaluator

When the source is an OpenSpec archive directory:

**Date derivation:**
- Use the archive folder name date if it follows `YYYY-MM-DD-*` pattern
- Otherwise use the latest date found inside the documents (design.md dates, proposal dates)
- Fallback: file modification time

**Status determination:**
- Identify which spec(s) the change relates to (from proposal.md's Capabilities section)
- Check if those specs still exist in the project's `openspec/specs/` directory
- If spec exists → status: `active`
- If spec was removed → status: `inactive`
- If unclear → status: `uncertain`

**Where to look for decisions:**
- `design.md` — Decisions section is the primary source (these ARE architectural decisions by definition)
- `proposal.md` — Impact section may contain structural constraints
- `adr.md` or `adr/*.md` — if the archive already produced ADRs, note them for dedup

---

## Section B: Default Evaluator

When the source is a general document (vault doc, research-discuss, meeting notes, etc.):

**Date derivation:**
- Look for explicit dates in frontmatter (`created:`, `date:`, `modified:`)
- Look for dates within the content near the decision ("decided on 2026-06-06")
- Use git history (`git log --follow -- <path>`) for first-commit date
- Fallback: file modification time

**Status determination:**
- Attempt to verify the decision is still active:
  - If the decision mentions specific code/files: grep for them to confirm they still exist
  - If the decision mentions specific specs: check `openspec/specs/`
  - If neither is possible → status: `uncertain`
- Candidates with `uncertain` status pass through (human approves/rejects later)

**Where to look for decisions:**
- Scan for patterns: "we do X because Y", "X not Y because Z", "decided to...", "the rule is..."
- Look for sections named: "Architecture", "Decisions", "Design choices", "Key rules", "Constraints"
- In research-discuss files: Claude's blockquoted recommendations that the user confirmed

---

## Output Schema

Each candidate is a structured object:

```
title:        Short decision title (e.g., "Park tasks rather than delete")
status:       active | inactive | uncertain
date:         YYYY-MM-DD (best estimate of when decision was made)
context:      1-3 sentences: what prompted this decision
decision:     1-3 sentences: the choice, stated clearly
consequences: 1-3 sentences: what becomes easier/harder
confidence:   high | medium | low
reasoning:    Why this was identified as an ADR candidate (which criteria matched)
source_ref:   Path to source file + section/line if identifiable
target_scope: The target adr/ path (echoed from input)
```

**Confidence mapping:**
- `high` — all 4 criteria clearly met, explicit "we chose X over Y" language
- `medium` — 3 criteria met, or all 4 but phrasing is implicit
- `low` — 2 criteria met, or decision may be implementation detail

---

## What this skill does NOT do

- Does NOT write ADR files
- Does NOT update INDEX.md
- Does NOT ask for user approval
- Does NOT modify any files

It produces a candidate list. The calling skill (`add-adrs-from-file` or `bootstrap-adrs`) handles everything after evaluation.
