## MODIFIED Requirements

### Requirement: add-adrs-from-file is a plugin skill with frontmatter
The `add-adrs-from-file` skill SHALL be located at `skills/add-adrs-from-file/SKILL.md` with YAML frontmatter including a `description` field.

#### Scenario: User invokes add-adrs-from-file
- **WHEN** the user invokes `/osadr:add-adrs-from-file`
- **THEN** the skill runs the single-source ADR extraction workflow as currently specified

#### Scenario: Skill invokes evaluate-file-for-adrs
- **WHEN** the skill needs to evaluate a source for ADR candidates
- **THEN** it invokes `/osadr:evaluate-file-for-adrs` (the subskill)
