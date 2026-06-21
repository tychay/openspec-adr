## Why

The PostToolUse `prompt` hook from `enforce-init-hooks` fires on every Bash command and outputs verbose text explaining why it doesn't apply. This pollutes the conversation, slows interaction, and wastes tokens. The hook should be silent except when `openspec init` was actually run.

## What Changes

- Replace the PostToolUse `prompt` type hook with a `command` type that exits silently (exit 0) when the command wasn't `openspec init`
- When it IS `openspec init`: either output a reminder to check schema (current fix), or run a script that auto-fixes config.yaml and copies the schema directory (future enhancement)

## Capabilities

### New Capabilities
None.

### Modified Capabilities
- `init-enforcement-hooks`: PostToolUse hook changes from noisy prompt to silent command

## Impact

- `hooks/hooks.json` — PostToolUse entry rewritten
- No other files affected
