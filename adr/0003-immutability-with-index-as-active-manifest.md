# 0003. ADR immutability with INDEX.md as active manifest

- Status: accepted
- Date: 2026-06-14

## Context

ADR orthodoxy requires immutability (supersede, never edit). In long-lived projects this creates Pareto chains (10% of decisions occupy 90% of supersession chains). We needed a pragmatic compromise that preserves audit history without requiring chain-traversal to find current state.

Extracted from [[adr-integration-research]], originally decided ~2026-06-14.

## Decision

ADR files are immutable once accepted. Changed decisions create new superseding ADRs. Each `adr/` directory contains an `INDEX.md` listing currently-in-force ADRs — this is the "HEAD pointer" Claude reads.

## Consequences

- Positive: audit history preserved, no chain-following needed (INDEX.md is authoritative for current state)
- Negative: INDEX.md must be maintained alongside ADR writes (extra bookkeeping)
- Neutral: superseded ADRs stay on disk unchanged as historical record
