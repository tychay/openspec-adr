## Why

AI agents (Claude) create `openspec/` directories via `mkdir` instead of `openspec init`, resulting in missing config files, missing schema definitions, and the wrong default schema being used. This happened across 6 submodules before being caught. Hooks are the correct enforcement mechanism — they intercept the bad behavior without burning context tokens on rules that the AI must "remember."

## What Changes

- Add a `hooks/` directory to the plugin with a `hooks.json` file containing two hooks:
  - **PreToolUse (Bash):** Block commands that `mkdir` an `openspec` directory — message instructs to use `openspec init` instead
  - **PostToolUse (Bash):** After `openspec init` completes, prompt to verify that `openspec/config.yaml` has `schema: spec-driven-with-adr` and fix it if not
- Document the known bug (anthropics/claude-code#10875) where plugin hook STDOUT may not be captured, and provide workaround guidance

## Capabilities

### New Capabilities
- `init-enforcement-hooks`: Plugin hooks that prevent manual openspec directory creation and enforce schema configuration after init

### Modified Capabilities
None — existing skills/specs are unchanged.

## Impact

- New `hooks/` directory in the plugin (native Claude Code plugin feature)
- No changes to existing skills, rules, or ADRs
- Plugin consumers get automatic enforcement once the plugin is installed — no per-project setup needed
- Workaround needed if anthropics/claude-code#10875 affects hook delivery (documented in design)
