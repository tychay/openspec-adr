---
description: Install the spec-driven-with-adr schema with broken-schema repair for a project
disable-model-invocation: true
---

# Setup

Configure the current project for the ADR workflow.

## Steps

1. **Detect environment** — check which instruction files exist at the project root:
   - `CLAUDE.md` exists → proceed
   - `AGENTS.md` (without CLAUDE.md) → report "AGENTS.md integration not yet supported" and exit
   - None found → ask the user if they want to proceed anyway

2. **Install schema (with repair loop)** — detect, repair, and install `spec-driven-with-adr`:

   a. Run `openspec schema which spec-driven-with-adr`
   b. If found: read the `schema.yaml` at the reported path. Check if any artifact's
      `generates` field contains `../../../adr` (the broken marker).
      If broken: delete the entire schema directory at the reported path.
      Report "Deleted broken schema at <path> (generates field pointed at shared adr/)".
   c. Repeat from (a) — loop until the schema is correctly installed or not found.
   d. If not found: install from upstream using the AGENT_INSTALL process:
      - Read `https://raw.githubusercontent.com/intent-driven-dev/openspec-schemas/refs/heads/main/AGENT_INSTALL.md`
      - Follow its instructions to install schema `spec-driven-with-adr`
   e. If found and correct: report "Schema already installed (correct)" and continue.

3. **Delete stale INDEX.md** — if `adr/INDEX.md` exists at the project root:
   ```bash
   rm adr/INDEX.md
   ```
   Report "Deleted stale adr/INDEX.md (superseded by ADR 0012)".
   If it does not exist, proceed without action.

4. **Report** — tell the user setup is complete. Mention:
   - Schema installed (or already present, or repaired)
   - INDEX.md cleaned up (or not present)
   - Skills available: `/osadr:bootstrap-adrs` for initial population, `/osadr:add-adrs-from-file` for ongoing extraction
