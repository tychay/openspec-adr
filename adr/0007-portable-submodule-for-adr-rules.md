# 0007. Portable submodule at ai/openspec-adr/

- Status: accepted
- Date: 2026-06-15

## Context

ADR rules, schema, and extraction skills need to be shared across projects (this project, tychay-yelp-GTL, potentially others). Options: playbook doc in root, or a git submodule that propagates updates.

Extracted from [[adr-integration-research]], originally decided ~2026-06-15.

## Decision

ADR rules, schema files, and extraction skills live in `ai/openspec-adr/` as a git submodule. Even if content stays light, the structure enforces componentization and ensures updates propagate.

## Consequences

- Positive: rule updates propagate to all consuming projects, enforces clean boundaries
- Negative: submodule overhead (git operations slightly more complex)
- Neutral: same pattern as ai/maturity-ladder (proven, lightweight)
