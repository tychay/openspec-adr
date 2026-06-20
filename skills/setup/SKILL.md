---
description: Install the spec-driven-with-adr schema and create adr/INDEX.md for a project
disable-model-invocation: true
---

# Setup

Configure the current project for the ADR workflow.

## Steps

1. **Detect environment** — check which instruction files exist at the project root:
   - `CLAUDE.md` exists → proceed
   - `AGENTS.md` (without CLAUDE.md) → report "AGENTS.md integration not yet supported" and exit
   - None found → ask the user if they want to proceed anyway

2. **Install schema** — check if `spec-driven-with-adr` is already available:
   ```bash
   openspec schema which spec-driven-with-adr
   ```
   If it reports a path, the schema is already installed — continue.
   
   If not found: Read this file: `https://raw.githubusercontent.com/intent-driven-dev/openspec-schemas/refs/heads/main/README.md` and install schema `spec-driven-with-adr`.

3. **Create adr/INDEX.md** — if `adr/INDEX.md` does not exist at the project root:
   ```bash
   mkdir -p adr
   ```
   Then create `adr/INDEX.md` with:
   ```markdown
   # Active ADRs

   Currently-in-force architectural decisions for this scope.

   <!-- Format: - [NNNN-title](NNNN-title.md) — one-line summary -->
   ```
   If it already exists, report "adr/INDEX.md already present" and continue.

4. **Report** — tell the user setup is complete. Mention:
   - Schema installed (or already present)
   - adr/INDEX.md ready (or already present)
   - Skills available: `/osadr:bootstrap-adrs` for initial population, `/osadr:add-adrs-from-file` for ongoing extraction
