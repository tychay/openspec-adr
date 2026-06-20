## 1. Rename and restructure repo

- [ ] 1.1 Rename GitHub repo from `ai-openspec-adr` to `openspec-adr`
- [ ] 1.2 Create `.claude-plugin/plugin.json` (name: `osadr`, version: `1.0.0`, description, author)
- [ ] 1.3 Create `skills/understand-adr-rules/SKILL.md` with full ADR workflow rules content from `CLAUDE-RULES.md`
- [ ] 1.4 Move `.claude/skills/bootstrap-adrs.md` → `skills/bootstrap-adrs/SKILL.md` (add YAML frontmatter with description)
- [ ] 1.5 Move `.claude/skills/add-adrs-from-file.md` → `skills/add-adrs-from-file/SKILL.md` (add YAML frontmatter)
- [ ] 1.6 Move `.claude/skills/evaluate-file-for-adrs.md` → `skills/evaluate-file-for-adrs/SKILL.md` (add YAML frontmatter)
- [ ] 1.7 Create `skills/setup/SKILL.md` (schema install + adr/INDEX.md creation, `disable-model-invocation: true`)
- [ ] 1.8 Update skill cross-references to use `/osadr:` namespace and invoke understand-adr-rules where needed

## 2. Remove old structure

- [ ] 2.1 Delete `CLAUDE-RULES.md` (content moved to understand-adr-rules)
- [ ] 2.2 Delete `schemas/` directory (setup installs from registry)
- [ ] 2.3 Delete `.claude/` directory (skills moved to `skills/`)

## 3. Documentation

- [ ] 3.1 Update `README.md` for plugin installation (marketplace add, plugin install, /osadr:setup)
- [ ] 3.2 Commit and push to GitHub (under new repo name `openspec-adr`)

## 4. Marketplace and install (delivery)

- [ ] 4.1 Clone `openspec-adr` into `~/Developer/my-local-claudecode-marketplace/plugins/openspec-adr`
- [ ] 4.2 Add to `marketplace.json` (name: `osadr`, source: `./plugins/openspec-adr`)
- [ ] 4.3 `claude plugin install osadr@my-local-claudecode-marketplace --scope user`
- [ ] 4.4 Replace cache directory with symlink to the marketplace clone (for live dev)
- [ ] 4.5 Reload Window, verify `/osadr:setup` is discoverable

## 5. Dev environment (parent project)

- [ ] 5.1 Create `external-dev/openspec-adr` symlink → marketplace clone
- [ ] 5.2 Remove `ai/openspec-adr` submodule (git rm + update .gitmodules)
- [ ] 5.3 Update parent CLAUDE.md (remove `ai/openspec-adr/CLAUDE-RULES.md` reference)
- [ ] 5.4 Update Spec Locality table in CLAUDE.md (add `external-dev/openspec-adr` row)

## 6. Validation

- [ ] 6.1 Run `/osadr:setup` and verify schema installs + adr/INDEX.md created
- [ ] 6.2 Invoke `/osadr:understand-adr-rules` and verify rules load
- [ ] 6.3 Invoke `/osadr:bootstrap-adrs` and verify it's discoverable and runs
- [ ] 6.4 Verify the plugin's own `adr/` and `openspec/` directories are intact
