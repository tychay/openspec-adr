# 0008. Extraction skills are markdown instructions, not TypeScript

- Status: accepted
- Date: 2026-06-16

## Context

ADR extraction requires reading prose, judging whether content meets the ADR heuristic, comparing candidates for overlap — all AI reasoning tasks. A hybrid approach (TypeScript for file I/O, AI for judgment) was considered but rejected because Claude already has file I/O tools and the overhead of maintaining a runner isn't justified.

Extracted from adr-extraction-bootstrapping-skill design.md, originally decided ~2026-06-16.

## Decision

ADR extraction skills are `.md` instruction files in `.claude/skills/`, not TypeScript code. No runtime code, no build step. The value IS the AI reasoning — the skill file captures the rubric and workflow.

## Consequences

- Positive: zero build overhead, directly editable, portable with the submodule
- Negative: no programmatic testing of skill behavior (must test by running)
- Neutral: consistent with AI maturity ladder (AI justified for structuring/judging tasks)
