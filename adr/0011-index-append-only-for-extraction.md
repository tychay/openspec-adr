# 0011. INDEX.md is append-only for extraction skills

- Status: accepted
- Date: 2026-06-16

## Context

Extraction skills write new ADRs to `adr/` directories. The INDEX.md update could either regenerate the full index (read all files, rebuild) or simply append new entries. Since extraction skills only produce active ADRs (inactive candidates are pruned before write), there's nothing to remove.

Extracted from [[adr-integration-research]], originally decided ~2026-06-16.

## Decision

Extraction skills only append new entries to INDEX.md. They never regenerate, reorder, or remove existing entries. Removal of superseded entries is a separate manual action (handled by the normal openspec change flow when a superseding ADR is written).

## Consequences

- Positive: simple, predictable, no risk of reformatting user-edited entries
- Negative: if entries are manually misordered, extraction won't fix them
- Neutral: consistent with only-active-ADRs design constraint
