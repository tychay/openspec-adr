# 0001. Use spec-driven-with-adr over intent-driven

- Status: accepted
- Date: 2026-06-14

## Context

The Intent Driven Dev community provides two ADR-capable schemas: `spec-driven-with-adr` (additive ADR step, same spec format we already use) and `intent-driven` (mandatory ADR, Gherkin specs). We needed to choose which philosophy to adopt for our solo dev + AI workflow operating in brown-field.

Extracted from [[adr-integration-research]], originally decided ~2026-06-14.

## Decision

Use `spec-driven-with-adr`. The ADR step is additive and skippable ("adr-last"), not a gate. We do not use `intent-driven`.

## Consequences

- Positive: minimal delta from existing workflow, works in brown-field, no forced ADR overhead for trivial changes
- Negative: intent gaps possible (may skip ADR for changes that actually had architectural consequence)
- Neutral: can always extract ADRs later from design.md archives ("adr-last" safety net)
