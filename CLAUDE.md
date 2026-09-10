# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is a single Claude Code **skill package** (`repo-deep-dive`), not an application. There is no build, lint, or test tooling — the repository consists of `SKILL.md` (the methodology Claude follows when the skill is invoked) and `README.md` (install/usage instructions for humans). There is no other code in this repo.

The skill is installed by cloning this repo into `~/.claude/skills/repo-deep-dive`, where Claude Code auto-discovers it via `SKILL.md`. There is no packaging or publish step beyond committing changes to this repo.

## Working on this repo means editing `SKILL.md`

Any task here is almost always "improve the methodology in `SKILL.md`." Treat `SKILL.md` as the deliverable, not as documentation of separate code. Read it in full before editing — the two-phase structure (Phase 1: `repo_summary.md`; Phase 2: isolated replica + incremental `study_plan.md`) is deliberately designed and cross-referenced, so changes to one part (e.g. calibration questions) often need to stay consistent with later parts (e.g. output-language handling in Phase 2's per-section templates).

## The hard rule (from `SKILL.md` itself)

`SKILL.md` explicitly states, at its end, that it must stay generic:

> This file is meant to be reused across many different, unrelated codebases. When updating it after using it somewhere, genericize any lesson learned before it lands here — never let a specific project's real names, business details, technology choices, people, internal tools, or findings leak into this file.

When asked to fold a lesson learned from using this skill on some other codebase back into this repo, strip out anything specific to that codebase (names, stacks, org details) before writing it into `SKILL.md`. This rule applies with equal force to `README.md` and to this `CLAUDE.md` file.

## Structural conventions to preserve when editing `SKILL.md`

- **Calibration first**: the skill always asks about the reader's background *and* explicitly confirms output language (English or Spanish) before producing any deliverable — never infer either from the request's own language.
- **Sensitivity check**: before anything could become visible beyond the reader (publishing, committing critique, or feeding a lesson back into this skill file), sensitivity must be confirmed.
- **Bilingual per-section templates in Phase 2**: the two fixed template variants (English/Español) for the study-plan sections must stay in sync with each other structurally — if a field is added/removed in one, mirror it in the other. Do not invent a third phrasing or mix languages within one document.
- **Incremental disclosure in Phase 2**: only the *active* study-plan section gets the full template (reference files, notebook name, supporting material); every other section gets a one-line summary only. This pacing is intentional, not an inefficiency to fix.
- **Replica isolation**: the methodology requires the learning replica to live in a separate directory, never touch the original repo's tracked files without separate explicit approval, and never contain real production code, credentials, or internal URLs copied wholesale.
