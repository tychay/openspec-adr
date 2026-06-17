# Active ADRs

Currently-in-force architectural decisions for the openspec-adr submodule.

- [0001-spec-driven-with-adr-over-intent-driven](0001-spec-driven-with-adr-over-intent-driven.md) — Use spec-driven-with-adr schema; ADR step is additive/skippable, not a gate
- [0002-adrs-at-repo-root-not-inside-openspec](0002-adrs-at-repo-root-not-inside-openspec.md) — ADR files at adr/ parallel to openspec/, per submodule
- [0003-immutability-with-index-as-active-manifest](0003-immutability-with-index-as-active-manifest.md) — ADRs immutable once accepted; INDEX.md is the HEAD pointer
- [0004-skip-adr-when-no-architectural-consequence](0004-skip-adr-when-no-architectural-consequence.md) — Skip ADR step for changes without architectural decisions
- [0005-schema-preference-via-config](0005-schema-preference-via-config.md) — Schema selection in openspec config, not CLAUDE.md
- [0006-claude-rules-is-process-index-is-data](0006-claude-rules-is-process-index-is-data.md) — CLAUDE-RULES.md = process rules; INDEX.md = per-scope active list
- [0007-portable-submodule-for-adr-rules](0007-portable-submodule-for-adr-rules.md) — ai/openspec-adr/ as git submodule for portability
- [0008-extraction-skills-are-markdown-not-code](0008-extraction-skills-are-markdown-not-code.md) — Extraction skills are .md instruction files, not TypeScript
- [0009-factory-pattern-via-instruction-branching](0009-factory-pattern-via-instruction-branching.md) — One skill file with conditional sections per evaluator type
- [0010-markdown-based-approval-ux](0010-markdown-based-approval-ux.md) — Candidate approval as markdown document, not interactive prompts
- [0011-index-append-only-for-extraction](0011-index-append-only-for-extraction.md) — Extraction skills append to INDEX.md, never regenerate
