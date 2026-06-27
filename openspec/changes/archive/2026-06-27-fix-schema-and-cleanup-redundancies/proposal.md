## Why

A prior session modified the `spec-driven-with-adr` schema's ADR artifact to use `generates: "../../../adr/*.md"` — a glob pointing at the shared `adr/` directory. This causes the CLI to always find pre-existing ADR files and report the artifact as `done` before the step ever runs, permanently skipping ADR review for every change after the first. The plugin also accumulated redundant copies of standard openspec skills/commands and a broken `INDEX.md` mechanism that conflicts with the correct per-change `adr.md` manifest.

## What Changes

- **Delete `.claude/skills/openspec-*/` and `.claude/commands/opsx/`** — redundant copies of standard openspec skills that the parent project already provides via `openspec init`. Having local copies creates drift risk.
- **Update `skills/setup/SKILL.md` Step 2** — add a repair loop that detects broken schemas (hallmark: `generates` field containing `../../../adr`) and deletes them before installing from upstream. This makes setup self-healing for repos that already have the broken schema installed.
- **Replace `skills/setup/SKILL.md` Step 3** — instead of creating `adr/INDEX.md`, setup now *deletes* it if present. This makes setup self-cleaning: running it in any repo removes the stale artifact automatically.
- **Update `hooks/hooks.json` PostToolUse** — remove the advisory that says "verify openspec/schemas/spec-driven-with-adr/ exists" after `openspec init`. This encouraged keeping broken local schema copies.

## Capabilities

### New Capabilities

- `setup`: The setup skill that configures a project for the ADR workflow — environment detection, schema installation with repair loop (detect and delete broken schemas before installing from upstream), and schema validation.

### Modified Capabilities

(none)

## Impact

- **Plugin skill files**: `skills/setup/SKILL.md` rewritten
- **Plugin hooks**: `hooks/hooks.json` simplified
- **Redundant files removed**: `.claude/skills/openspec-*/`, `.claude/commands/opsx/`, `adr/INDEX.md`
- **Downstream repos**: After this fix, running `/osadr:setup` in any repo with the broken schema will detect and repair it automatically
- **ADR 0003** (immutability-with-index-as-active-manifest): Will be superseded by a new ADR — the INDEX.md mechanism is retired in favor of the upstream per-change `adr.md` manifest
