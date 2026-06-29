## Why

The `/osadr:setup` skill currently only operates on the "current project" (the project root where the Claude Code session is running). It also has `disable-model-invocation: true`, meaning only the user can invoke it directly. This prevents two legitimate use cases: (1) an orchestrator session that needs to set up ADR infrastructure in a different project directory, and (2) AI-driven workflows that need to call setup as part of a larger automated sequence.

## What Changes

- Remove `disable-model-invocation: true` from the setup skill frontmatter, allowing AI invocation
- Add an optional target directory argument to the setup skill so it can operate on a directory other than the project root
- When a target directory is provided, all operations (environment detection, schema installation, INDEX.md cleanup) target that directory instead of the project root

## Capabilities

### New Capabilities

(none — this modifies an existing capability)

### Modified Capabilities

- `setup`: Adding target directory parameter and removing model-invocation restriction

## Impact

- **`skills/setup/SKILL.md`**: Frontmatter change (remove `disable-model-invocation`) and step modifications to support target directory argument
- **Backward compatible**: When invoked without arguments, behavior is identical to current (operates on project root)
