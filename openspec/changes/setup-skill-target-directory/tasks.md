## 1. Skill frontmatter

- [x] 1.1 Remove `disable-model-invocation: true` from `skills/setup/SKILL.md` frontmatter

## 2. Target directory support

- [x] 2.1 Add argument handling to SKILL.md: if args provided, use as target directory; otherwise default to project root (CWD)
- [x] 2.2 Add step 0: verify target directory exists and is accessible (report error and exit if not)
- [x] 2.3 Update step 1 (environment detection): check for `CLAUDE.md` / `AGENTS.md` at target directory, not project root
- [x] 2.4 Update step 2 (schema install): run `openspec` commands from target directory context (prefix with `cd <target> &&` if needed)
- [x] 2.5 Update step 3 (INDEX.md cleanup): check/delete `<target>/adr/INDEX.md` instead of project root

## 3. Verification

- [x] 3.1 Test: invoke skill without arguments and confirm behavior is unchanged
- [x] 3.2 Test: invoke skill with a valid target directory path
- [x] 3.3 Test: invoke skill with a nonexistent path and confirm error handling
