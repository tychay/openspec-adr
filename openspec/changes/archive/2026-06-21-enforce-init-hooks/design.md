## Context

Claude Code plugins support a `hooks/` directory with a `hooks.json` file. Hooks fire on events (PreToolUse, PostToolUse, SessionStart, PostCompact, Stop) with matchers and typed actions (`command` or `prompt`). Reference implementation: `tychay-yelp-GTL/tools/claude-obsidian/hooks/hooks.json`.

Known issue: anthropics/claude-code#10875 — plugin hook STDOUT may not be captured in some Claude Code versions. Identical hooks in `~/.claude/settings.json` work correctly. This means `command` type hooks that rely on exit code 2 (block) may silently fail.

## Goals / Non-Goals

**Goals:**
- Prevent AI from creating `openspec/` directories with `mkdir` (force `openspec init`)
- After `openspec init` runs, ensure schema is set to `spec-driven-with-adr`
- Zero ongoing token cost — hooks fire automatically without polluting context

**Non-Goals:**
- Blocking the user from manually running `mkdir` (hooks only affect AI tool use)
- Enforcing schema on `openspec new change --schema spec-driven` (that's the intentional override)
- Replacing the `bin/setup-rules` pattern (hooks are native; rules still need symlinks)

## Decisions

**1. Use `prompt` type actions, not `command` type**

Due to the STDOUT capture bug (#10875), `command` hooks that exit 2 to block may silently fail when delivered via plugin. Using `prompt` type instead injects the instruction into the AI's context at the moment it matters — right before/after the relevant tool call. This is reliable regardless of the bug.

Trade-off: `prompt` can't hard-block (the AI could ignore it), but it's injected at exactly the right moment with clear instructions. Given that the AI is the actor being corrected (not the user), a well-timed prompt is sufficient.

**2. PreToolUse matcher: `Bash`**

Match all Bash tool calls. The prompt checks if the command contains `mkdir` + `openspec` and instructs to abort and use `openspec init` instead. Matcher is broad (`Bash`) because we can't pattern-match command content in the matcher — the prompt does the content check.

**3. PostToolUse matcher: `Bash`**

Match Bash calls after execution. The prompt checks if the command was `openspec init` and instructs to verify/fix the schema in the resulting config.yaml.

## Risks / Trade-offs

- [Prompt ignored] The AI might not follow the injected prompt. Mitigation: the prompt is clear, specific, and injected at the exact moment. In practice, AI agents follow injected system prompts reliably.
- [Bug #10875 workaround] If prompt-type hooks are also affected by the bug, the fallback is documenting that users should copy the hooks into their project's `.claude/settings.json`. The README will include this workaround.
- [Broad matcher] Matching all Bash calls adds a tiny check on every command. The prompt itself is short (~50 words) so context cost is minimal and only present during that one tool call.
