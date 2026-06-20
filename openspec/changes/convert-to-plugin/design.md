## Context

The `ai/openspec-adr` submodule currently provides ADR workflow rules and 3 extraction skills via a `.claude/skills/` directory nested inside the submodule. Skills are discovered by Claude Code's recursive search. The rules (`CLAUDE-RULES.md`) are referenced by path from the parent's CLAUDE.md. The schema (`spec-driven-with-adr`) is manually copied into each project's `openspec/schemas/`.

The `aiml` plugin conversion (completed 2026-06-19) established the pattern: marketplace distribution, `bin/setup-rules` for symlinked rules, skills in `skills/<name>/SKILL.md`, and `disable-model-invocation: true` for setup commands. This change follows the same pattern where applicable but differs in that ADR rules don't need every-session loading.

**In-force ADRs (this repo):**
- 0007: Portable submodule for ADR rules
- 0008: Extraction skills are markdown, not code
- 0009: Factory pattern via instruction branching (evaluator dispatch)

## Goals / Non-Goals

**Goals:**
- Plugin discovery of all skills (no more nested `.claude/skills/` reliance)
- Marketplace distribution (installable via `claude plugin install`)
- Schema installation automated via setup skill (no manual copy)
- ADR workflow rules accessible on-demand (not every session)
- Consistent with aiml plugin patterns
- Preserve evaluator extensibility path (factory pattern in evaluate-file-for-adrs)

**Non-Goals:**
- Evaluator modularization (future change — companion plugin for Yelp-specific evaluators)
- OpenCode plugin support (aware of portability, not building for it)
- Changing the ADR workflow rules content itself
- Migrating the plugin's own ADRs or openspec specs (they stay in place)

## Decisions

### D1: No `rules/` symlink — ADR rules load on demand via skill

Unlike the aiml plugin (7 short lines, shapes all behavior), the ADR rules are 80+ lines of process documentation only relevant during ADR work. Loading them every session wastes context.

The `understand-adr-rules` skill holds the content. Skills that need the rules invoke it. This mirrors aiml's `understand-ladder` pattern.

Alternative: symlink into `.claude/rules/` (aiml pattern). Rejected — too heavy for every session, only relevant during ADR-specific workflows.

### D2: No `bin/` needed

The setup skill's work is: (1) run `openspec schema install spec-driven-with-adr`, (2) create `adr/INDEX.md`. Both are single Bash commands Claude can run directly from the skill instructions. No wrapper script needed.

Alternative: `bin/setup-adr` script. Rejected — overhead for two one-liners.

### D3: Schema installed from registry, not bundled

The `schemas/` directory is removed. `/osadr:setup` runs `openspec schema install spec-driven-with-adr` which pulls from the OpenSpec schema registry. This means the schema updates independently of the plugin.

Alternative: keep a bundled copy. Rejected — creates drift between the registry version and the bundled version; the registry is the source of truth.

Risk: if the schema isn't in the registry, setup fails. Mitigation: verify the registry has it before removing the bundled copy. If not, push it upstream first.

### D4: Plugin name `osadr`, repo name `openspec-adr`

The namespace `osadr` gives `/osadr:bootstrap-adrs`, `/osadr:setup`, etc. Short, parallels `opsx` (OpenSpec eXecute). The git repo drops the `ai-` prefix since that was the old namespace convention.

Alternative: `ops-adr`, `adr`. Rejected — `ops-adr` could be confused with opsx; `adr` is too generic for a marketplace.

### D5: Flat skills get frontmatter and directory structure

Current `.claude/skills/bootstrap-adrs.md` becomes `skills/bootstrap-adrs/SKILL.md` with YAML frontmatter (`description`, and for evaluate-file-for-adrs: `user-invocable: false` to mark it as a subskill).

### D6: Submodule removal happens AFTER plugin is installed and verified

The `ai/openspec-adr` submodule stays until the plugin is working. Removal is the last step — commit, install plugin, verify setup works, THEN remove submodule. This prevents any gap where ADR skills are unavailable.

## Risks / Trade-offs

**[Schema not in registry]** → Setup fails if `openspec schema install spec-driven-with-adr` has no upstream source. Mitigation: verify registry has it before removing `schemas/`. If not, push to registry first or keep a fallback copy.

**[Skill invocation rename]** → Users calling `/bootstrap-adrs` must switch to `/osadr:bootstrap-adrs`. Mitigation: the old `.claude/skills/` path goes away with the submodule, so the old names stop working regardless. The new names are consistent.

**[evaluate-file-for-adrs as subskill]** → If marked `user-invocable: false`, users can't invoke it directly. But the proposal says it's usable standalone ("just checking what ADRs a document contains"). Resolution: keep it user-invocable but note in description that it's typically called by other skills.

**[No uninstall]** → Same as aiml: no teardown script. If user uninstalls the plugin, the schema remains installed (harmless) and `adr/INDEX.md` remains (it's project data, not plugin data).

## Open Questions

- Is `spec-driven-with-adr` actually in the OpenSpec schema registry? Need to verify before removing `schemas/`.
- Should `evaluate-file-for-adrs` be user-invocable or subskill-only? The current docs say both.
