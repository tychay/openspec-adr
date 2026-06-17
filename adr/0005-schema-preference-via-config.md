# 0005. Schema preference driven by config, not CLAUDE.md

- Status: accepted
- Date: 2026-06-15

## Context

Confusion arose about where the "prefer spec-driven-with-adr" logic should live. Options: CLAUDE.md rule, maturity-ladder rule, or openspec config. The maturity-ladder already says "Use OpenSpec/SDD" without specifying which schema — that decision belongs to the config file.

Extracted from [[adr-integration-research]], originally decided ~2026-06-15.

## Decision

Schema selection is determined by `openspec/config.yaml` (`schema: spec-driven-with-adr`), not by CLAUDE.md rules. Fallback is automatic: if the schema isn't installed, config says `spec-driven` and Claude uses the standard flow.

## Consequences

- Positive: CLAUDE.md doesn't need to know about ADR, config is the single source of truth for schema
- Negative: new projects must remember to set the config (covered by install instructions)
- Neutral: existing "Use OpenSpec/SDD" maturity-ladder rule works unchanged
