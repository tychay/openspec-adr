# ADR Review Manifest

## ADR Review Completed

- Date: 2026-06-27
- Reviewer: Claude
- Change: fix-schema-and-cleanup-redundancies

## In-Force ADR Context Reviewed

- adr/0001-spec-driven-with-adr-over-intent-driven.md — confirms we use this schema (still valid)
- adr/0002-adrs-at-repo-root-not-inside-openspec.md — ADRs stay at repo root (still valid)
- adr/0003-immutability-with-index-as-active-manifest.md — INDEX.md mechanism (NOW SUPERSEDED by 0012)
- adr/0004-skip-adr-when-no-architectural-consequence.md — skip logic (still valid)
- adr/0005-schema-preference-via-config.md — config-driven schema (still valid)
- adr/0006-claude-rules-is-process-index-is-data.md — process/data separation (principle still valid, INDEX.md detail is historical)
- adr/0007-portable-submodule-for-adr-rules.md — submodule structure (still valid)
- adr/0008-extraction-skills-are-markdown-not-code.md — markdown skills (still valid)
- adr/0009-factory-pattern-via-instruction-branching.md — instruction branching (still valid)
- adr/0010-markdown-based-approval-ux.md — approval UX (still valid)
- adr/0011-index-append-only-for-extraction.md — INDEX.md append-only (NOW SUPERSEDED by 0012)

## Repository-Level ADRs Created

- adr/0012-per-change-manifest-over-global-index.md — retires INDEX.md; per-change adr.md manifest is the correct completion marker; extraction skills no longer maintain a global index

## Notes

This change was caused by a regression where the schema's `generates` field was changed to match INDEX.md files, permanently skipping ADR review. The fix restores the upstream schema and retires the INDEX.md mechanism entirely.
