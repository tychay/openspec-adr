## Context

The `enforce-init-hooks` change added a PostToolUse `prompt` hook matching all Bash commands. This fires on every single Bash tool call, injecting ~50 words into the conversation that the AI then responds to with an explanation of why it doesn't apply. Result: every Bash command generates noise.

## Goals / Non-Goals

**Goals:**
- PostToolUse hook is completely silent when the command is not `openspec init`
- When the command IS `openspec init`, output a brief reminder or auto-fix

**Non-Goals:**
- Changing the PreToolUse hook (that one only fires on mkdir+openspec, which is rare)
- Auto-fixing schema without any output (we want visibility when it acts)

## Decisions

**Use `command` type with early exit:**

```json
{
  "type": "command",
  "command": "echo \"$TOOL_INPUT\" | grep -q 'openspec init' || exit 0; <action>"
}
```

`exit 0` produces no output and no conversation injection. Only when the grep matches does the command proceed to output anything. This is the minimal-token approach.

## Risks / Trade-offs

- [TOOL_INPUT format] Relies on `$TOOL_INPUT` containing the command string as JSON with a `.command` field accessible via grep. If the format changes, the hook silently stops working (fails open, not closed — acceptable).
