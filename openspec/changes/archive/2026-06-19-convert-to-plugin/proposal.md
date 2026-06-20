## Why

The `ai/openspec-adr` submodule uses a bespoke `ai/` namespace convention that predates Claude Code's plugin system. Its skills are discovered via nested `.claude/skills/` search (fragile), its rules require a manual CLAUDE.md path reference (breaks with marketplace distribution), and its schema requires a manual copy step. Converting to a proper plugin fixes discovery, enables marketplace distribution, and aligns with the pattern established by the `aiml` plugin conversion.

## What Changes

- Convert the git repo from `ai-openspec-adr` to a Claude Code plugin named `osadr`
- Rename the GitHub repo from `ai-openspec-adr` to `openspec-adr`
- Move skills from `.claude/skills/*.md` (flat files) to `skills/<name>/SKILL.md` (plugin standard)
- Create `skills/understand-adr-rules/SKILL.md` containing the CLAUDE-RULES.md content (loaded on demand by other skills)
- Create `skills/setup/SKILL.md` that installs the schema from registry and creates `adr/INDEX.md`
- Remove `CLAUDE-RULES.md` (content moved to understand-adr-rules skill)
- Remove `schemas/` directory (setup installs from OpenSpec schema registry)
- Remove `.claude/` directory (skills moved to plugin-standard `skills/`)
- Deliver via local marketplace, install at user scope, symlink cache for live dev
- Remove `ai/openspec-adr` submodule from parent project after plugin is installed and verified

## Capabilities

### New Capabilities
- `plugin-manifest`: The `.claude-plugin/plugin.json` manifest (name: `osadr`) enabling marketplace distribution and namespaced skill discovery
- `understand-adr-rules`: A skill that loads the full ADR workflow rules into context on demand (format spec, when-to-produce heuristic, immutability rule, schema preference). Other skills invoke this when they need the rules.
- `plugin-setup`: A `/osadr:setup` skill that installs the `spec-driven-with-adr` schema from the registry, creates `adr/INDEX.md` if absent, and reports configuration status

### Modified Capabilities
- `bootstrap-adrs`: Moved to plugin skill structure, add frontmatter for discovery, invoke understand-adr-rules for workflow context
- `add-adrs-from-file`: Moved to plugin skill structure, add frontmatter for discovery
- `evaluate-file-for-adrs`: Moved to plugin skill structure, add frontmatter, marked as subskill (not user-invoked directly)

## Impact

- GitHub repo renamed: `ai-openspec-adr` → `openspec-adr`
- Parent project: `ai/openspec-adr` submodule removed, replaced by marketplace-installed plugin
- Parent CLAUDE.md: reference to `ai/openspec-adr/CLAUDE-RULES.md` removed (rules load on demand via skill)
- Spec Locality table: new row for `external-dev/openspec-adr`
- `.gitmodules`: `ai/openspec-adr` entry removed
- Local marketplace `marketplace.json`: new entry for `osadr`
- Skills invocation changes: `/bootstrap-adrs` → `/osadr:bootstrap-adrs`, etc.
