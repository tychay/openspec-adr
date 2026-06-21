## 1. Replace hooks with command type

- [x] 1.1 Change PreToolUse from `prompt` to `command` type — read stdin JSON, extract command, check mkdir target ends with openspec dirs, exit 2 to block or exit 0 silently
- [x] 1.2 Change PostToolUse from `prompt` to `command` type — read stdin JSON, exit 0 silently unless command was `openspec init`

## 2. Verification

- [x] 2.1 Test PreToolUse blocks `mkdir /path/openspec/specs` (new openspec dir)
- [x] 2.2 Test PreToolUse passes `mkdir specs/foo` within existing openspec structure
- [x] 2.3 Test PostToolUse is silent on normal Bash commands
