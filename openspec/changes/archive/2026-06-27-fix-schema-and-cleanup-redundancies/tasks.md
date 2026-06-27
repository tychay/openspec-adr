## 1. Remove redundant files

- [x] 1.1 Delete `.claude/skills/openspec-apply-change/`
- [x] 1.2 Delete `.claude/skills/openspec-archive-change/`
- [x] 1.3 Delete `.claude/skills/openspec-explore/`
- [x] 1.4 Delete `.claude/skills/openspec-propose/`
- [x] 1.5 Delete `.claude/commands/opsx/`

## 2. Fix hooks

- [x] 2.1 Update PostToolUse hook in `hooks/hooks.json` — keep config schema check, remove "verify openspec/schemas/spec-driven-with-adr/ exists" part

## 3. Rewrite setup skill

- [x] 3.1 Rewrite `skills/setup/SKILL.md` Step 2 to include repair loop: run `openspec schema which`, read schema.yaml, check for `../../../adr` in generates field, delete if broken, loop until clean or not-found, then install from upstream if needed
- [x] 3.2 Replace Step 3 in `skills/setup/SKILL.md`: instead of creating INDEX.md, delete `adr/INDEX.md` if it exists (report "Deleted stale adr/INDEX.md")
- [x] 3.3 Update skill description frontmatter to remove INDEX.md reference

## 4. Update extraction skills to remove INDEX.md references

- [x] 4.1 Update `skills/add-adrs-from-file/SKILL.md` to remove INDEX.md append step (per ADR 0012)
- [x] 4.2 Update `skills/bootstrap-adrs/SKILL.md` to remove INDEX.md append step (per ADR 0012)

## 5. Verify via setup re-run

- [x] 5.1 Run the updated setup skill on this repo — it should: find schema correct, delete `adr/INDEX.md`, report done
- [x] 5.2 Run `openspec schema validate` to confirm schema still valid
- [x] 5.3 Create a test change, confirm `adr` artifact shows as `blocked` (not `done`), delete test change
