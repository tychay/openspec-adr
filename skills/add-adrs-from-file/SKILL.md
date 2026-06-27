---
description: Evaluate a source file for ADR candidates, present for approval, and write approved ADRs to the target directory
---

# add-adrs-from-file

Evaluate a source file for ADR candidates, present for approval, and write
approved ADRs to the target directory.

If you need the full ADR format spec and heuristic, invoke `/osadr:understand-adr-rules`.

---

## Inputs

1. **Source path**: File or folder to evaluate
2. **Target adr/ path**: Where to write approved ADRs

---

## Workflow

### Step 1: Evaluate

Invoke `/osadr:evaluate-file-for-adrs` with the source path and target adr/ path.
This produces a candidate list.

If zero candidates are found → report "No ADR candidates found in <source>" and exit.

### Step 2: Present for approval

Present the candidates as a markdown list for the user to review. Format:

```markdown
## ADR Candidates from <source>
Target: <target adr/ path>

### 1. <title> [confidence: high]
- **Status:** active
- **Date:** 2026-06-06
- **Decision:** <1-3 sentences>
- **Reasoning:** <why this was identified>
- **Verdict:** ✅ APPROVE / ❌ REJECT

### 2. <title> [confidence: medium]
- **Status:** uncertain
- **Date:** 2026-05-30
- **Decision:** <1-3 sentences>
- **Reasoning:** <why this was identified>
- **Verdict:** ✅ APPROVE / ❌ REJECT
```

Default verdicts:
- `high` confidence → default APPROVE
- `medium` confidence → default APPROVE
- `low` confidence → default REJECT

Tell the user: "Review the candidates above. Change any verdicts you disagree with, then confirm to proceed with writing."

Wait for user confirmation before proceeding.

### Step 3: Write approved ADRs

For each approved candidate:

**Determine next number:**
- Read existing files in target `adr/` directory
- Find the highest `NNNN` prefix
- Start new ADRs at the next number

**Write ADR file:**

Filename: `NNNN-kebab-title.md`

Content:
```markdown
# NNNN. <Decision title>

- Status: accepted
- Date: <date from candidate>

## Context

<context from candidate>

Extracted from [[<source filename without extension>]], originally decided ~<date>.

## Decision

<decision from candidate>

## Consequences

<consequences from candidate>
```

**Write files in date order** (oldest first) so numbering reflects chronology.

---

## Error Handling

- If target `adr/` directory doesn't exist → STOP. Tell user: "Target directory <path> does not exist. Create it first (or run `/osadr:setup`)."
- If all candidates are rejected → report "All candidates rejected, no ADRs written." and exit.
