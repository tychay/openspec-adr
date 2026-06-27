---
description: Interactive workflow for initially populating an adr/ directory from existing documentation — evaluates multiple sources, deduplicates, and writes the final set
---

# bootstrap-adrs

Interactive workflow for populating an `adr/` directory from existing
documentation. Orchestrates `/osadr:evaluate-file-for-adrs` across multiple sources,
deduplicates, and writes the final set.

If you need the full ADR format spec and heuristic, invoke `/osadr:understand-adr-rules`.

---

## Inputs

1. **Target project path**: The project/submodule whose `adr/` to populate (e.g., `tools/things-sync`)

---

## Workflow

### Step 1: Identify target and confirm adr/ exists

Resolve the target `adr/` path (e.g., `tools/things-sync/adr/`).

If `adr/` doesn't exist → STOP. Tell user to create it first (or run `/osadr:setup`).

### Step 2: Build source list (interactive)

Suggest likely sources for the target project:
- Vault docs that mention the project (search for wikilinks to it)
- OpenSpec archives in the project's `openspec/changes/` (look for archived dirs)
- Research-discuss files related to the project
- The project's own documentation (README, CLAUDE.md with decision-like content)

Present the suggested list to the user. Ask them to confirm, add, or remove sources.

Wait for user confirmation before proceeding.

### Step 3: Evaluate each source

For each confirmed source, invoke `/osadr:evaluate-file-for-adrs` with:
- Source: the file/folder
- Target: the target `adr/` path

Accumulate all candidates across sources into a single list.

Report progress: "Evaluated <source>: found N candidates (running total: M)"

### Step 4: Remove inactive candidates

Remove any candidate with `status: inactive` from the list. These are decisions
that are no longer in force and should not be written.

Report: "Pruned N inactive candidates. M remain."

### Step 5: Cross-source deduplication

Compare candidates across all sources. When the same decision appears from
multiple sources:
- Keep the instance with the earliest date
- Discard duplicates

Two candidates are "the same decision" if their `decision` fields describe
the same architectural choice, even if worded differently.

Report: "Deduped N duplicates. M unique candidates remain."

### Step 6: Overlap and supersession check

Compare remaining candidates pairwise:
- If candidate A is a weaker/earlier form of candidate B → keep only B (or both with B noting supersession)
- If two candidates partially overlap but are distinct enough → keep both
- If two candidates contradict each other → flag for user decision

Report any contradictions or supersession relationships found.

### Step 7: Present for approval

Present the final candidate list in markdown format (same format as `/osadr:add-adrs-from-file` Step 2).

Default verdicts based on confidence. Wait for user confirmation.

### Step 8: Write in date order

Sort approved candidates by date (oldest first).

Number sequentially starting from the next available number in the target `adr/` directory.

For each candidate:
1. Write `NNNN-kebab-title.md` with standard ADR format
2. Include provenance in Context section: "Extracted from [[<source>]], originally decided ~<date>."

Report: "Wrote N ADRs to <target adr/ path>."

---

## Running bootstrap for multiple targets

To bootstrap an entire project with multiple `adr/` directories, run this
workflow once per target. Suggested order: start with the simplest/most recent
target first to validate the process, then proceed to larger targets.

The workflow is idempotent per target — running it again on the same target will
find no new candidates (existing ADRs are filtered in the dedup step).

---

## Differences from add-adrs-from-file

| Aspect | add-adrs-from-file | bootstrap-adrs |
|--------|-------------------|----------------|
| Sources | Single file | Multiple files (interactive list) |
| Dedup | Against existing ADRs only | Cross-source + against existing |
| Overlap check | None | Pairwise supersession detection |
| Inactive pruning | None (subskill reports, skill presents) | Automatic removal |
| Ordering | Date order within single source | Date order across all sources |
| Use case | Ad hoc, ongoing | Initial population |
