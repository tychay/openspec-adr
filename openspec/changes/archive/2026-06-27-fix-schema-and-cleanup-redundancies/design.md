## Context

The `openspec-adr` plugin is a Claude Code marketplace plugin that adds ADR workflow support to projects using OpenSpec. It installs as a git submodule (typically at `external-dev/openspec-adr` or `ai/openspec-adr`) and provides skills for setup, ADR extraction, and bootstrapping.

A prior session introduced two regressions:
1. Modified the `spec-driven-with-adr` schema's ADR artifact `generates` field from `adr.md` to `"../../../adr/*.md"`, causing the ADR step to be permanently skipped (pre-existing files always match the glob).
2. Introduced `adr/INDEX.md` as a "HEAD pointer" for in-force ADRs. This was conceptually interesting but wrong — the upstream schema uses a per-change `adr.md` manifest that correctly tracks which ADRs were reviewed per-change. INDEX.md created a global mutable file that conflated per-change tracking with cross-change state.

The schema has already been fixed (Stage 0 — deleted broken local copy, installed from upstream). This change fixes the plugin's skills and hooks to not re-introduce the problem and to repair it in other repos.

**In-force ADRs relevant to this design:**
- ADR 0001: Use spec-driven-with-adr schema (still valid)
- ADR 0002: ADRs at repo root `adr/`, not inside openspec (still valid)
- ADR 0003: INDEX.md as active manifest — **this design proposes superseding this**
- ADR 0006: CLAUDE-RULES.md is process, INDEX.md is data — partially affected (the process/data split is sound, but INDEX.md as the data carrier is retired)
- ADR 0011: INDEX.md append-only for extraction — affected (extraction skills will no longer append to INDEX.md)

## Goals / Non-Goals

**Goals:**
- Make the setup skill self-healing: detect broken schemas and fix them
- Remove the INDEX.md mechanism entirely from setup
- Remove redundant openspec skill/command copies that drift from the parent project
- Clean up hooks that encouraged broken behavior
- Ensure the repair loop can propagate to all affected repos

**Non-Goals:**
- Changing how the extraction skills (`add-adrs-from-file`, `bootstrap-adrs`) work beyond removing INDEX.md writes — that's a separate change
- Modifying the upstream schema itself
- Fixing other repos in this change (that happens by running the updated setup skill later)

## Decisions

### 1. Repair loop via generates-field inspection

**Decision:** Detect broken schemas by reading the schema.yaml and checking if any `generates` field contains `../../../adr`. If found, delete the entire schema directory and re-check (loop until clean or not-found).

**Why this over alternatives:**
- Version comparison: The upstream schema has no version field that distinguishes "broken local" from "correct upstream." The generates path IS the signal.
- Hash comparison: Fragile across upstream updates. The broken marker (`../../../adr`) is stable and specific.
- Just delete always: Would re-download on every setup run even when the schema is correct. The check is cheap.

### 2. Delete redundant .claude/skills and .claude/commands

**Decision:** Remove `.claude/skills/openspec-*/` and `.claude/commands/opsx/` entirely. These are copies of standard openspec skills that the parent project gets from `openspec init`.

**Why:** Local copies in the plugin create drift risk — when upstream openspec skills update, the plugin's copies become stale. The plugin should only contain its UNIQUE skills (in `skills/`), not copies of standard infrastructure.

### 3. Remove PostToolUse hook for schema verification

**Decision:** Remove the PostToolUse hook that advises "verify openspec/schemas/spec-driven-with-adr/ exists" after detecting `openspec init`.

**Why:** This hook encouraged maintaining a local schema copy rather than relying on the upstream-installed one. The repair loop in setup handles correctness; the hook is counterproductive.

### 4. Retire INDEX.md mechanism — setup deletes it

**Decision:** Replace the INDEX.md creation step in setup with an INDEX.md *deletion* step. If `adr/INDEX.md` exists, setup deletes it. This makes setup idempotent and self-cleaning — running it in any repo with the stale artifact removes the confusion.

**Why:** INDEX.md conflated two things:
- Per-change tracking ("which ADRs did I review for THIS change?") — handled by `adr.md` manifest
- Cross-change state ("what's currently in force?") — derivable by reading `adr/` files and walking Supersedes links (exactly what the design artifact instruction already describes)

INDEX.md was a shortcut that broke the workflow's completion detection. Leaving stale INDEX.md files around is actively harmful — they mislead Claude into thinking the mechanism still operates. Making setup delete them means:
1. For this repo: the verify/cleanup step just re-runs setup (idempotent)
2. For downstream repos: running `/osadr:setup` both fixes the schema AND cleans up INDEX.md in one shot — no separate cleanup sweep needed

## Risks / Trade-offs

- [Extraction skills still reference INDEX.md] → The `bootstrap-adrs` and `add-adrs-from-file` skills currently append to INDEX.md. After this change, INDEX.md won't exist. Mitigation: those skills should gracefully handle missing INDEX.md (they'll need a follow-up change to remove that logic, but they won't crash — they use "if INDEX.md exists, append").
- [Repos with existing INDEX.md] → Handled: setup now deletes INDEX.md if found. Running `/osadr:setup` propagates the fix.
- [Upstream schema changes] → If the upstream schema changes its structure, the repair loop's detection heuristic (`../../../adr` in generates) remains valid since that pattern was never correct upstream.

## Migration Plan

1. This change fixes the plugin itself
2. After merge, run `/osadr:setup` in each affected repo — the repair loop fixes broken schemas AND deletes stale INDEX.md in one pass

## Open Questions

- ADR 0003 needs superseding (INDEX.md as active manifest is retired)
- ADR 0006 and 0011 reference INDEX.md — 0006's principle (process/data separation) remains valid but the data carrier changes; 0011 (append-only extraction) becomes moot without INDEX.md. These may warrant supersession or may be left as historical context. The ADR step will decide.
