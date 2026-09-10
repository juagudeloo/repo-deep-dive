# repo-deep-dive

A Claude Code skill for deeply understanding an unfamiliar codebase, in two phases:

1. A calibrated, verified `repo_summary.md` — purpose, stack, architecture, key
   algorithms, and honest technical-debt findings.
2. An isolated learning replica with an incremental study plan: notebooks you
   write yourself, one section at a time, with Claude preparing only the
   supporting material for whichever section is currently active.

See [SKILL.md](SKILL.md) for the full methodology.

## Install

Clone this repo into your Claude Code skills directory:

```bash
git clone git@github.com:juagudeloo/repo-deep-dive.git ~/.claude/skills/repo-deep-dive
```

Claude Code picks up any skill under `~/.claude/skills/<name>/SKILL.md`
automatically — no further setup needed.

## Use

In a Claude Code session, inside any repo you want to understand:

```
/repo-deep-dive
```

or just describe what you want in your own words (e.g. "help me understand
this codebase and build a study plan for it") — the skill's description
covers those trigger phrases too.
