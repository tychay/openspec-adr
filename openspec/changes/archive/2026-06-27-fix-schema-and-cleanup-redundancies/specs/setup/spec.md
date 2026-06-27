## ADDED Requirements

### Requirement: Environment detection

The setup skill SHALL detect which instruction files exist at the project root before proceeding.

#### Scenario: CLAUDE.md present
- **WHEN** `CLAUDE.md` exists at the project root
- **THEN** setup proceeds to schema installation

#### Scenario: AGENTS.md without CLAUDE.md
- **WHEN** `AGENTS.md` exists but `CLAUDE.md` does not
- **THEN** setup reports "AGENTS.md integration not yet supported" and exits

#### Scenario: No instruction files
- **WHEN** neither `CLAUDE.md` nor `AGENTS.md` exists
- **THEN** setup asks the user if they want to proceed anyway

### Requirement: Schema repair loop

The setup skill SHALL detect and delete broken schema installations before installing from upstream.

#### Scenario: Broken schema detected
- **WHEN** `openspec schema which spec-driven-with-adr` reports a path
- **AND** the `schema.yaml` at that path contains a `generates` field with `../../../adr` in any artifact
- **THEN** the setup skill deletes the entire schema directory at the reported path
- **AND** repeats detection from the beginning

#### Scenario: Correct schema already installed
- **WHEN** `openspec schema which spec-driven-with-adr` reports a path
- **AND** the `schema.yaml` at that path does NOT contain `../../../adr` in any generates field
- **THEN** setup reports "schema already installed (correct)" and proceeds

#### Scenario: Schema not found
- **WHEN** `openspec schema which spec-driven-with-adr` reports no path (not found)
- **THEN** setup installs the schema from upstream using the AGENT_INSTALL process

### Requirement: Upstream schema installation

The setup skill SHALL install the `spec-driven-with-adr` schema from the upstream openspec-schemas repository when needed.

#### Scenario: Fresh install from upstream
- **WHEN** the schema is not found locally
- **THEN** setup clones `https://github.com/intent-driven-dev/openspec-schemas.git` to a temporary location
- **AND** copies `openspec/schemas/spec-driven-with-adr/` from the clone into the project's `./openspec/schemas/`
- **AND** runs `openspec schema validate` to confirm the installation

#### Scenario: Validation failure
- **WHEN** `openspec schema validate` fails after installation
- **THEN** setup reports the validation error and stops

### Requirement: INDEX.md cleanup

The setup skill SHALL delete `adr/INDEX.md` if it exists. The per-change `adr.md` manifest is the correct mechanism for tracking ADR review; stale INDEX.md files are actively harmful.

#### Scenario: INDEX.md exists
- **WHEN** `adr/INDEX.md` exists at the project root
- **THEN** setup deletes it
- **AND** reports "Deleted stale adr/INDEX.md (superseded by ADR 0012)"

#### Scenario: INDEX.md does not exist
- **WHEN** `adr/INDEX.md` does not exist
- **THEN** setup proceeds without action
