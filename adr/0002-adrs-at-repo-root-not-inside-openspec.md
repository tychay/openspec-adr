# 0002. ADRs live at repo root adr/, not inside openspec/

- Status: accepted
- Date: 2026-06-14

## Context

ADRs outlive any single openspec change. They have a different lifecycle than specs (immutable vs evolving) and serve a different audience (future decision-makers vs implementers). The schema's output path (`../../../adr/*.md`) enforces separation.

Extracted from [[adr-integration-research]], originally decided ~2026-06-14.

## Decision

ADR files live at `adr/` parallel to `openspec/`, not inside it. Each submodule with its own openspec gets its own `adr/` directory at its root.

## Consequences

- Positive: clear lifecycle separation, ADRs survive openspec directory restructuring
- Negative: two directories to maintain per project scope
- Neutral: directory structure is simple and predictable across all projects
