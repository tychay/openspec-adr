## MODIFIED Requirements

### Requirement: bootstrap-adrs is a plugin skill with frontmatter
The `bootstrap-adrs` skill SHALL be located at `skills/bootstrap-adrs/SKILL.md` with YAML frontmatter including a `description` field. It SHALL invoke `/osadr:understand-adr-rules` when it needs ADR format and heuristic context.

#### Scenario: User invokes bootstrap-adrs
- **WHEN** the user invokes `/osadr:bootstrap-adrs`
- **THEN** the skill runs the interactive ADR extraction workflow as currently specified

#### Scenario: Skill needs ADR rules context
- **WHEN** the skill needs to apply the 4-point ADR heuristic or format spec
- **THEN** it invokes `/osadr:understand-adr-rules` to load the rules
