## MODIFIED Requirements

### Requirement: Block manual openspec directory creation
The plugin SHALL include a PreToolUse hook that detects Bash commands creating an openspec directory via mkdir and blocks execution with exit 2.

#### Scenario: AI runs mkdir targeting openspec
- **WHEN** a Bash tool call contains `mkdir` and the target path ends with `openspec/`, `openspec/specs`, `openspec/changes`, or `openspec/schemas`
- **THEN** the hook exits 2, blocking the command with a message to use `openspec init`

#### Scenario: mkdir within existing openspec structure
- **WHEN** a Bash tool call contains `mkdir` but the target path does NOT end with an openspec top-level directory (e.g., creating `specs/init-enforcement-hooks` within a change)
- **THEN** the hook exits 0 silently, allowing the command

#### Scenario: Unrelated mkdir command
- **WHEN** a Bash tool call does not contain `mkdir`
- **THEN** the hook exits 0 silently

### Requirement: Verify schema after openspec init
The plugin SHALL include a PostToolUse hook that detects `openspec init` execution and outputs a reminder, silent for all other commands.

#### Scenario: openspec init completes
- **WHEN** a Bash tool call containing `openspec init` completes
- **THEN** the hook outputs a reminder to verify schema is `spec-driven-with-adr`

#### Scenario: Any other Bash command
- **WHEN** a Bash tool call does NOT contain `openspec init`
- **THEN** the hook exits 0 with no output (completely silent)

### Requirement: Hooks use command type with stdin parsing
Both hooks SHALL use `command` type actions that read tool input from stdin (JSON with `tool_input.command` field), NOT `prompt` type actions.

#### Scenario: Hook receives input
- **WHEN** a hook fires
- **THEN** it reads stdin, parses JSON via jq to extract the command, and evaluates silently
