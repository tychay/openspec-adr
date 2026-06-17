# 0004. Skip ADR step when no architectural consequence

- Status: accepted
- Date: 2026-06-15

## Context

The schema makes ADR a mandatory step in the artifact flow. Without a skip rule, every change — including 1-10 line bug fixes — would require an ADR stub file. This creates noise that obscures real architectural decisions.

Extracted from [[adr-integration-research]], originally decided ~2026-06-15.

## Decision

The ADR artifact step is skippable. No stub files for changes without architectural consequence. The 4-point heuristic determines when to produce an ADR: constrains future work, has rejected alternatives, outlives the change, is non-obvious. If none apply, skip entirely.

## Consequences

- Positive: no busywork for trivial changes, signal-to-noise ratio stays high
- Negative: possible missed ADRs for changes that seemed trivial but had hidden consequences
- Neutral: missed ADRs can be caught later via extraction skills ("adr-last" philosophy)
