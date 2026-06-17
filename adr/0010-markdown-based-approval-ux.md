# 0010. Markdown-based approval UX

- Status: accepted
- Date: 2026-06-16

## Context

After evaluation, ADR candidates need user approval before writing. Options: interactive prompts (confirm each), or markdown document for batch review. User explicitly prefers text-based review in files and the approval step is at the end of the workflow, making it easy to swap interfaces later.

Extracted from [[adr-integration-research]], originally decided ~2026-06-16.

## Decision

Candidate approval is a markdown document presented to the user. Each candidate shows title, status, confidence, date, reasoning, and a verdict (APPROVE/REJECT). User reviews in their editor and confirms.

## Consequences

- Positive: user can review holistically, doesn't depend on Claude's interactive prompt capabilities
- Negative: slightly more steps than a quick "y/n" prompt
- Neutral: interface is swappable since approval is the last step before write
