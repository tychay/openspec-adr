## ADDED Requirements

### Requirement: Setup skill installs schema from registry
The plugin SHALL contain a `skills/setup/SKILL.md` (invoked as `/osadr:setup`) that installs the `spec-driven-with-adr` schema from the OpenSpec schema registry.

#### Scenario: User invokes setup in a project with openspec
- **WHEN** the user invokes `/osadr:setup` in a project that has `openspec/`
- **THEN** it runs `openspec schema install spec-driven-with-adr` to install the schema

#### Scenario: Schema already installed
- **WHEN** the schema is already present in `openspec/schemas/spec-driven-with-adr/`
- **THEN** setup reports "schema already installed" and continues

### Requirement: Setup creates adr/INDEX.md if absent
The setup skill SHALL create an `adr/` directory with an `INDEX.md` file if the project does not already have one.

#### Scenario: Project has no adr/ directory
- **WHEN** setup runs and no `adr/` directory exists at the project root
- **THEN** it creates `adr/INDEX.md` with the standard empty template

#### Scenario: Project already has adr/INDEX.md
- **WHEN** setup runs and `adr/INDEX.md` already exists
- **THEN** it reports "already configured" and does not modify it

### Requirement: Setup is user-invoked only
The setup skill SHALL have `disable-model-invocation: true` so Claude does not auto-trigger it.

#### Scenario: Claude encounters ADR-related work
- **WHEN** Claude is working on a task that involves ADRs
- **THEN** it does NOT auto-invoke `/osadr:setup` — the user must run it explicitly
