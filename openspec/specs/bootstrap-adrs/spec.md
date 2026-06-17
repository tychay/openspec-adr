## ADDED Requirements

### Requirement: Interactive source list building
The bootstrap workflow SHALL interactively build a list of source files/folders to evaluate for a given target project's `adr/` directory.

#### Scenario: User provides target project
- **WHEN** invoked with a target project path (e.g., `tools/things-sync`)
- **THEN** the workflow prompts the user to identify relevant source documents for that project

#### Scenario: AI suggests sources
- **WHEN** the target project is identified
- **THEN** the workflow suggests likely sources (vault docs mentioning the project, openspec archives, related research-discuss files) for user confirmation

### Requirement: Serial execution across sources
The workflow SHALL process each source file sequentially using `evaluate-file-for-adrs`, accumulating candidates.

#### Scenario: Multiple sources processed
- **WHEN** the source list contains 3 files
- **THEN** each is evaluated in order, candidates accumulate across all sources

### Requirement: Cross-source deduplication
The workflow SHALL deduplicate candidates across all sources before the write step, accepting the oldest instance of a duplicate decision.

#### Scenario: Same decision found in two sources
- **WHEN** things-sync-workflow and an openspec archive both yield "parking not deleting"
- **THEN** only the one with the earlier date is kept

### Requirement: Cross-candidate overlap check
The workflow SHALL check accumulated candidates for overlap or supersession relationships before writing.

#### Scenario: One candidate supersedes another
- **WHEN** candidate A (older) is a weaker form of candidate B (newer)
- **THEN** only candidate B is kept, or both are kept with B noting it supersedes A

### Requirement: Inactive candidates are pruned automatically
The workflow SHALL remove candidates marked `inactive` without requiring user approval.

#### Scenario: Inactive candidate from OpenSpec evaluator
- **WHEN** a candidate's related spec no longer exists
- **THEN** it is removed from the candidate list before approval

### Requirement: Batch write in date order
The workflow SHALL write all approved ADRs in chronological order by decision date, numbered sequentially.

#### Scenario: Bootstrap produces 8 ADRs
- **WHEN** 8 candidates are approved with dates ranging from 2026-05-27 to 2026-06-14
- **THEN** they are written as 0001 through 0008, ordered by date, and INDEX.md is updated

### Requirement: Workflow is portable via submodule
The bootstrap workflow skill SHALL live in `ai/openspec-adr/` and be usable in any project that includes that submodule.

#### Scenario: Used in a different project
- **WHEN** `ai/openspec-adr/` is included as a submodule in another project
- **THEN** the bootstrap workflow is available and functional without modification
