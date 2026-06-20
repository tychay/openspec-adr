## ADDED Requirements

### Requirement: Plugin manifest exists at .claude-plugin/plugin.json
The repo SHALL contain a `.claude-plugin/plugin.json` file declaring plugin identity with name `osadr`.

#### Scenario: Plugin is installed via marketplace
- **WHEN** a user runs `claude plugin install osadr@my-local-claudecode-marketplace`
- **THEN** Claude Code registers the plugin and its skills are discoverable under the `/osadr:` namespace

#### Scenario: Plugin manifest contains required fields
- **WHEN** `.claude-plugin/plugin.json` is parsed
- **THEN** it contains `name` ("osadr"), `version`, `description`, and `author`
