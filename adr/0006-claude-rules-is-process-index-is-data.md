# 0006. CLAUDE-RULES.md is process, INDEX.md is per-scope data

- Status: accepted
- Date: 2026-06-15

## Context

An earlier design conflated two concerns: operational rules (how ADR works) and active ADR pointers (which ADRs are in force). This created circular logic where CLAUDE-RULES.md would contain both the process AND the data for one specific scope.

Extracted from [[adr-integration-research]], originally decided ~2026-06-15.

## Decision

Separate concerns cleanly: `ai/openspec-adr/CLAUDE-RULES.md` contains operational rules (process — when to write ADRs, format, skip logic). Each `adr/INDEX.md` contains the active ADR list (data — per project/submodule). Root CLAUDE.md only references CLAUDE-RULES.md with a one-liner.

## Consequences

- Positive: no circular references, each file has one job, scales to any number of adr/ directories
- Negative: slightly more indirection (CLAUDE.md → CLAUDE-RULES.md → INDEX.md)
- Neutral: same pattern as ai/maturity-ladder/CLAUDE-RULES.md (proven)
