## ADDED Requirements

### Requirement: Block manual openspec directory creation
The plugin SHALL include a PreToolUse hook that detects Bash commands creating an openspec directory via mkdir and instructs the AI to use `openspec init` instead.

#### Scenario: AI runs mkdir openspec
- **WHEN** a Bash tool call contains a command with `mkdir` and `openspec` in the same command string
- **THEN** the injected prompt instructs the AI to abort and instead cd into the target project and run `openspec init --tools claude --force`

#### Scenario: AI runs mkdir -p with openspec path
- **WHEN** a Bash tool call contains `mkdir -p` with a path containing `openspec` (e.g., `mkdir -p foo/openspec/specs`)
- **THEN** the injected prompt instructs the AI to abort and use `openspec init`

#### Scenario: Unrelated mkdir command
- **WHEN** a Bash tool call contains `mkdir` but the path does not contain `openspec`
- **THEN** no intervention occurs (prompt is injected but recognizes the command is unrelated)

### Requirement: Verify schema after openspec init
The plugin SHALL include a PostToolUse hook that detects successful `openspec init` execution and instructs the AI to verify the schema configuration.

#### Scenario: openspec init completes
- **WHEN** a Bash tool call containing `openspec init` completes successfully
- **THEN** the injected prompt instructs the AI to read `openspec/config.yaml` and verify it has `schema: spec-driven-with-adr`. If it has `schema: spec-driven`, change it to `spec-driven-with-adr`.

#### Scenario: openspec init fails
- **WHEN** a Bash tool call containing `openspec init` exits with a non-zero status
- **THEN** no schema verification prompt is injected (the init didn't succeed)

### Requirement: Hooks delivered via plugin hooks.json
The hooks SHALL be defined in a `hooks/hooks.json` file within the plugin directory, using the Claude Code plugin hooks format.

#### Scenario: Plugin installed
- **WHEN** the openspec-adr plugin is installed in a project
- **THEN** the hooks are active without any additional setup steps

#### Scenario: Bug workaround documented
- **WHEN** hooks fail to fire due to anthropics/claude-code#10875
- **THEN** the plugin README documents how to copy the hooks into project `.claude/settings.json` as a workaround
