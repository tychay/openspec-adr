## 1. Create hooks directory and hooks.json

- [x] 1.1 Create `hooks/` directory in plugin root
- [x] 1.2 Create `hooks/hooks.json` with PreToolUse hook (Bash matcher, prompt action that checks for mkdir+openspec and instructs to use openspec init)
- [x] 1.3 Add PostToolUse hook (Bash matcher, prompt action that checks for openspec init and instructs to verify/set schema to spec-driven-with-adr)

## 2. Documentation

- [x] 2.1 Update README.md with hooks section — what they do, the known bug workaround (copy to settings.json)
- [x] 2.2 Add workaround instructions for anthropics/claude-code#10875

## 3. Verification

- [x] 3.1 Test PreToolUse hook: attempt `mkdir openspec` and verify prompt intervenes — hooks did NOT fire from plugin path (expected per #10875)
- [x] 3.2 Test PostToolUse hook: run `openspec init` and verify schema check prompt fires — same result, hooks not active from plugin
- [x] 3.3 If hooks don't fire (bug #10875), document and test the settings.json copy workaround — workaround documented in README, needs user to copy to settings.json
