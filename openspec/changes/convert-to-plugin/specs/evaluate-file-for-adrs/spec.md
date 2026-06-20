## MODIFIED Requirements

### Requirement: evaluate-file-for-adrs is a plugin skill with frontmatter
The `evaluate-file-for-adrs` skill SHALL be located at `skills/evaluate-file-for-adrs/SKILL.md` with YAML frontmatter including a `description` field. It remains user-invocable for standalone evaluation use.

#### Scenario: User invokes evaluate-file-for-adrs directly
- **WHEN** the user invokes `/osadr:evaluate-file-for-adrs`
- **THEN** the skill evaluates the given source for ADR candidates and returns the structured list without writing files

#### Scenario: Called as subskill by bootstrap-adrs or add-adrs-from-file
- **WHEN** another skill invokes `/osadr:evaluate-file-for-adrs`
- **THEN** it evaluates and returns the candidate list to the calling skill

### Requirement: Evaluator dispatch preserved for future extensibility
The skill SHALL preserve the evaluator factory pattern (detect source type → select OpenSpec evaluator or Default evaluator) to allow future companion plugins to add evaluators.

#### Scenario: OpenSpec archive directory evaluated
- **WHEN** the source is a directory containing `proposal.md` or `.openspec.yaml`
- **THEN** the OpenSpec evaluator (Section A) is used

#### Scenario: General document evaluated
- **WHEN** the source is not an OpenSpec archive
- **THEN** the Default evaluator (Section B) is used
