## ADDED Requirements

### Requirement: Skill wraps evaluate-file-for-adrs with approval and write
The skill SHALL invoke `evaluate-file-for-adrs`, present candidates for approval, and write approved ADRs to the target directory.

#### Scenario: Full flow from file to written ADRs
- **WHEN** invoked with a source file and target `adr/` directory
- **THEN** the skill evaluates, presents candidates, and after approval writes ADR files and updates INDEX.md

### Requirement: Markdown-based approval interface
The skill SHALL present candidates as a markdown document for the user to review. Each candidate includes its fields and a status the user can set (approve/reject).

#### Scenario: Candidates presented for review
- **WHEN** evaluation produces candidates
- **THEN** a markdown table or list is presented showing title, status, confidence, date, and reasoning for each candidate

#### Scenario: User approves subset
- **WHEN** user marks some candidates as approved and others as rejected
- **THEN** only approved candidates proceed to the write step

#### Scenario: No candidates found
- **WHEN** evaluation produces zero candidates
- **THEN** the skill reports "no ADR candidates found" and exits without writing

### Requirement: ADR file writing follows standard format
Written ADR files SHALL use the format: `NNNN-short-title.md` with the standard ADR template (Status, Date, Context, Decision, Consequences).

#### Scenario: Writing to empty directory (bootstrap)
- **WHEN** the target `adr/` has no existing ADRs
- **THEN** numbering starts at 0001

#### Scenario: Writing to populated directory (add-on)
- **WHEN** the target `adr/` already contains ADRs (e.g., 0001 through 0005)
- **THEN** new ADRs continue numbering from the next available number (0006)

### Requirement: INDEX.md is appended on write
The skill SHALL append newly written ADRs to the target `adr/INDEX.md` active list.

#### Scenario: New ADRs appended to index
- **WHEN** ADR files are written
- **THEN** each new entry is appended to INDEX.md with its number, title, and date

### Requirement: Provenance recorded in ADR body
Each written ADR SHALL include its extraction source in the Context section.

#### Scenario: Provenance from vault doc
- **WHEN** an ADR is extracted from a vault document
- **THEN** the Context section includes "Extracted from [[source-doc-name]]" with the approximate original decision date
