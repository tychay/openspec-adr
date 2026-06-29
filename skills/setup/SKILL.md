---
description: Install the spec-driven-with-adr schema with broken-schema repair for a project
---

# Setup

Configure a project for the ADR workflow.

## Arguments

If args are provided, use the args string as the target directory path. Otherwise default to the project root (CWD).

## Steps

0. **Verify target directory** — if a target directory was specified via args:
   - Check that the path exists and is a directory
   - If it does not exist or is not accessible, report "Error: target directory '<path>' does not exist or is not accessible" and stop

1. **Detect environment** — check which instruction files exist at the target directory:
   - `CLAUDE.md` exists → proceed
   - `AGENTS.md` (without CLAUDE.md) → report "AGENTS.md integration not yet supported" and exit
   - None found → ask the user if they want to proceed anyway

2. **Install schema (with repair loop)** — detect, repair, and install `spec-driven-with-adr`:

   a. Run `cd <target> && openspec schema which spec-driven-with-adr`
   b. If found: read the `schema.yaml` at the reported path. Check if any artifact's
      `generates` field contains `../../../adr` (the broken marker).
      If broken: delete the entire schema directory at the reported path.
      Report "Deleted broken schema at <path> (generates field pointed at shared adr/)".
   c. Repeat from (a) — loop until the schema is correctly installed or not found.
   d. If not found: install from upstream using the AGENT_INSTALL process:
      - Read `https://raw.githubusercontent.com/intent-driven-dev/openspec-schemas/refs/heads/main/AGENT_INSTALL.md`
      - Follow its instructions to install schema `spec-driven-with-adr` within the target directory
   e. If found and correct: report "Schema already installed (correct)" and continue.

3. **Delete stale INDEX.md** — if `adr/INDEX.md` exists at the target directory:
   ```bash
   rm <target>/adr/INDEX.md
   ```
   Report "Deleted stale adr/INDEX.md (superseded by ADR 0012)".
   If it does not exist, proceed without action.

4. **Report** — tell the user setup is complete. Mention:
   - Schema installed (or already present, or repaired)
   - INDEX.md cleaned up (or not present)
   - Skills available: `/osadr:bootstrap-adrs` for initial population, `/osadr:add-adrs-from-file` for ongoing extraction
