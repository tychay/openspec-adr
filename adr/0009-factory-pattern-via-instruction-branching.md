# 0009. Factory pattern via instruction branching

- Status: accepted
- Date: 2026-06-16

## Context

The `evaluate-file-for-adrs` skill needs to handle different source types (OpenSpec archives vs general documents) with different evaluation strategies. Options: separate skill files per evaluator type, or conditional sections within one skill file.

Extracted from adr-extraction-bootstrapping-skill design.md, originally decided ~2026-06-16.

## Decision

Use conditional sections in one skill file. The skill detects input characteristics and follows different evaluation paths based on what it finds. Adding a new evaluator type means adding a new section, not a new file.

## Consequences

- Positive: single entry point for callers, no need to know which evaluator to invoke
- Negative: skill file grows as evaluator types are added (may need splitting later)
- Neutral: future evaluators (techspec, cep, pr) follow the same pattern
