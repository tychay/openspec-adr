## ADDED Requirements

### Requirement: Subskill accepts a source file and target scope
The subskill SHALL accept two inputs: a source file path (or folder path) and a target scope identifier (the `adr/` directory path where candidates would be written).

#### Scenario: Single file input
- **WHEN** invoked with a single markdown file path and a target `adr/` directory
- **THEN** the subskill reads the file and evaluates it for ADR candidates

#### Scenario: Folder input
- **WHEN** invoked with a folder path (e.g., an openspec archive directory)
- **THEN** the subskill reads all relevant files in the folder and evaluates them collectively

### Requirement: Candidate extraction uses the 4-point heuristic
The subskill SHALL evaluate content against the ADR identification heuristic: (1) constrains future work, (2) has alternatives that were rejected, (3) outlives the change that introduced it, (4) is non-obvious.

#### Scenario: Content matches all four criteria
- **WHEN** a passage states "we do X because Y, not Z" and X constrains future work
- **THEN** the subskill produces a candidate with confidence `high`

#### Scenario: Content matches some criteria
- **WHEN** a passage describes a choice but alternatives aren't explicitly stated
- **THEN** the subskill produces a candidate with confidence `medium` or `low`

#### Scenario: Content is implementation detail
- **WHEN** a passage describes a how-to without architectural consequence
- **THEN** no candidate is produced

### Requirement: Output schema per candidate
Each candidate SHALL include: title, status (active|inactive|uncertain), date (YYYY-MM-DD best estimate), context, decision, consequences, confidence (high|medium|low), reasoning, source_ref, and target_scope.

#### Scenario: Complete candidate output
- **WHEN** an ADR candidate is identified
- **THEN** all schema fields are populated, with source_ref linking to the originating document

### Requirement: Factory pattern for evaluator selection
The subskill SHALL select an evaluator based on the input source type. An OpenSpec evaluator handles archived changes (using archive date and spec activity for status). A default evaluator handles arbitrary documents (using document dates, git history, code grep).

#### Scenario: OpenSpec archive input
- **WHEN** the source is an openspec archive directory containing proposal.md, design.md, tasks.md
- **THEN** the OpenSpec evaluator is used, deriving date from archive date and status from spec activity

#### Scenario: Vault document input
- **WHEN** the source is a general markdown file (not an openspec archive)
- **THEN** the default evaluator is used, deriving date from document metadata or content

### Requirement: Status determination for OpenSpec evaluator
The OpenSpec evaluator SHALL determine a candidate's status as `active` if the corresponding spec still exists in the project's `openspec/specs/` directory.

#### Scenario: Spec still active
- **WHEN** a candidate's related spec exists in `openspec/specs/`
- **THEN** candidate status is `active`

#### Scenario: Spec removed or absent
- **WHEN** a candidate's related spec does not exist in `openspec/specs/`
- **THEN** candidate status is `inactive` or `uncertain`

### Requirement: Filter against existing ADRs
The subskill SHALL read existing ADRs in the target `adr/` directory and exclude candidates that duplicate decisions already recorded.

#### Scenario: Candidate duplicates existing ADR
- **WHEN** a candidate's decision matches an existing ADR's decision in meaning
- **THEN** the candidate is excluded from output (or marked as duplicate in metadata)

#### Scenario: No existing ADRs
- **WHEN** the target `adr/` directory is empty (bootstrap case)
- **THEN** no filtering occurs and all candidates pass through
