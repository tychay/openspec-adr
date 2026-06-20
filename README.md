# OpenSpec ADR

A Claude Code plugin that integrates Architecture Decision Records into the OpenSpec spec-driven workflow. Provides extraction skills for populating ADRs from existing documentation and a setup command for project configuration.

## Installation

### Claude Code

Register the marketplace and install:
```bash
claude plugin marketplace add tychay/openspec-adr
claude plugin install osadr
```

### After install

Run the setup command to configure your project:
```
/osadr:setup
```

This installs the `spec-driven-with-adr` schema and creates `adr/INDEX.md` if needed.

## Skills

| Skill | Description |
|-------|-------------|
| `/osadr:setup` | Install schema and create adr/INDEX.md (one-time) |
| `/osadr:bootstrap-adrs` | Initial population: evaluate multiple sources, deduplicate, write |
| `/osadr:add-adrs-from-file` | Ongoing: extract ADRs from a single source |
| `/osadr:evaluate-file-for-adrs` | Evaluate a source for candidates without writing |
| `/osadr:understand-adr-rules` | Load the full ADR workflow rules into context |

## How it works

After setup, use `/osadr:bootstrap-adrs` for initial ADR population from existing docs, then `/osadr:add-adrs-from-file` as new decisions are made. The extraction skills apply a 4-point heuristic (constrains future work, has alternatives, outlives the change, non-obvious) and present candidates for approval before writing.

## Development

Assumes the local marketplace is already registered and the plugin is installed with the cache symlinked to this source directory.

**Dev loop:**

1. Edit source files directly in this directory
2. Pick up changes: **Cmd-Shift-P > "Developer: Reload Window"** (VSCode)
3. Validate structure: `claude plugin validate ./path/to/openspec-adr`

**Publishing changes:** Bump `version` in `plugin.json` before pushing — consumers pick up new versions via `claude plugin update`.
