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

## Hooks

This plugin ships two automatic hooks (no setup required after plugin install):

| Event | Trigger | Action |
|-------|---------|--------|
| PreToolUse (Bash) | Command contains `mkdir` + `openspec` | Prompts AI to use `openspec init` instead |
| PostToolUse (Bash) | Command was `openspec init` | Prompts AI to verify schema is `spec-driven-with-adr` |

### Workaround: anthropics/claude-code#10875

In some Claude Code versions, plugin hooks may not fire reliably. If hooks aren't working, copy the hook definitions into your project's `.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "CHECK: If the command you are about to run contains 'mkdir' and 'openspec' in the same command, STOP. Do not run it. Instead: cd into the target project directory and run 'openspec init --tools claude --force'. Never create openspec directories manually."
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "CHECK: If the command you just ran was 'openspec init', verify that openspec/config.yaml has 'schema: spec-driven-with-adr'. If it has 'schema: spec-driven', change it. Also verify openspec/schemas/spec-driven-with-adr/ exists."
          }
        ]
      }
    ]
  }
}
```

## Development

Assumes the local marketplace is already registered and the plugin is installed with the cache symlinked to this source directory.

**Dev loop:**

1. Edit source files directly in this directory
2. Pick up changes: **Cmd-Shift-P > "Developer: Reload Window"** (VSCode)
3. Validate structure: `claude plugin validate ./path/to/openspec-adr`

**Publishing changes:** Bump `version` in `plugin.json` before pushing — consumers pick up new versions via `claude plugin update`.
