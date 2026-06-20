## ADDED Requirements

### Requirement: understand-adr-rules skill provides ADR workflow rules on demand
The plugin SHALL contain a `skills/understand-adr-rules/SKILL.md` that loads the full ADR workflow rules into context when invoked by other skills or the user.

#### Scenario: Another skill needs the ADR rules
- **WHEN** `/osadr:bootstrap-adrs` or `/osadr:add-adrs-from-file` needs the ADR format spec or heuristic
- **THEN** it invokes `/osadr:understand-adr-rules` to load the rules into context

#### Scenario: User wants to understand ADR rules
- **WHEN** a user invokes `/osadr:understand-adr-rules`
- **THEN** the full ADR workflow rules are available in context (format, when-to-produce heuristic, immutability rule, schema preference, naming conventions)

### Requirement: understand-adr-rules contains all content from CLAUDE-RULES.md
The skill SHALL contain the complete operational rules content that was previously in `CLAUDE-RULES.md`, including the 4-point ADR heuristic, format spec, immutability rule, and schema preference section.

#### Scenario: No information loss from CLAUDE-RULES.md removal
- **WHEN** `CLAUDE-RULES.md` is removed from the plugin
- **THEN** all its content is accessible via the understand-adr-rules skill
