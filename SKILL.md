---
name: repo-deep-dive
description: Two-phase methodology for deeply understanding an unfamiliar codebase — (1) produce a calibrated, verified repo_summary.md (purpose, stack, architecture, key algorithms, technical debt), then (2) build an isolated learning replica with an incremental, section-by-section study plan of notebooks the user writes themselves, one section at a time, advancing only on explicit confirmation. Use when the user says things like "entender este repositorio", "hazme un resumen de este repo", "quiero aprender cómo funciona este código", "crea una réplica para estudiar esto", or otherwise wants to deeply learn an unfamiliar codebase rather than just get a task done in it.
---

# Repo Deep Dive

A two-phase method for going from "I don't know this codebase" to "I understand it well enough to have built it myself."

1. **Phase 1 — `repo_summary.md`**: a written, verified analysis, calibrated to what the reader already knows.
2. **Phase 2 — Replica + study plan**: an isolated learning sandbox with an ordered study plan, worked through one section at a time — the reader writes every notebook themselves; the assistant only prepares supporting material for whichever section is active.

Both phases apply to any codebase, any language, any domain. Nothing here is specific to one project — keep it that way when extending this file (see "Hard rule" at the bottom).

---

## Before starting: calibrate

Ask a short, targeted set of questions (not an essay) about what the reader already knows and doesn't, relative to what this repo will require — its language, its frameworks, its domain (e.g. "have you worked with distributed data processing before?", "do you know framework X?", "have you done ML before, or only heard the terms?"). Everything downstream — how much needs explaining, what deserves an appendix, what can be assumed — depends on this. Don't skip it, and don't guess from a resume alone; ask.

**Also explicitly confirm the output language** for every document this skill produces — `repo_summary.md`, `study_plan.md`, and the ongoing conversation itself. This skill is used by both Spanish- and English-speaking readers; don't infer it from the language of the reader's request alone (a bilingual reader may ask in one language and want the deliverable in the other), and don't silently default to English. Ask once, up front, and use that same language consistently everywhere afterward — including the per-section template headers in Phase 2 (see the note there).

Also confirm sensitivity up front: is this a work codebase, someone else's code with real names attached, or anything that shouldn't leave the reader's own machine? If there's any doubt, ask before doing anything that could make the analysis visible beyond the reader — publishing a document, committing candid criticism of colleagues' code to a shared repo, or folding a lesson learned from this specific codebase into a general-purpose skill file that gets reused elsewhere. When in doubt, keep it local and ask.

---

## Phase 1 — `repo_summary.md`

### Explore thoroughly; verify claims, don't guess

- Read the actual source, configs, CI definitions, and any existing docs — don't infer behavior from file or folder names alone.
- Do git archaeology when history is available: `git log --reverse --oneline`, and follow renames (`git log --follow <path>`) to see how the repo actually got to its current shape, not just what it looks like today. Retired or "legacy" directories often still hold artifacts worth recovering (old configs, training/build parameters, prior READMEs) even when the team believes nothing survived — check before taking that belief at face value.
- Verify factual claims by actually running something rather than asserting from memory or pattern-matching: count rows in a dataset, execute a function on a real sample, confirm a file the code assumes exists actually does. If a check contradicts something already said, correct it plainly and move on — don't bury the correction or restate it defensively.
- If the repo references an external source of truth that's reachable (an internal wiki page linked from a comment, a design doc, a ticket) and access is available, read it — the ambiguity code alone can't resolve is often already answered there.

### Structure (adapt the exact section names to the repo; the shape holds)

1. **Purpose** — the problem this solves, in plain terms, at the reader's calibrated level.
2. **Tech stack** — what's used, and *why*: is a choice deliberate, or a historical accident nobody revisited? Say which, when you can tell.
3. **Architecture** — the big-picture shape: what calls what, what depends on what, and where a seam exists (or should exist but doesn't).
4. **Key functions / algorithms** — the specific pieces of logic worth knowing by name, not an exhaustive file-by-file listing.
5. **(If applicable) The core model/algorithm/technique, from fundamentals** — if the repo's value sits in something the reader doesn't already know (a specific model architecture, a numerical method, a protocol), don't assume familiarity: build it up step by step from concepts the reader confirmed knowing in the calibration step. Prefer concrete examples and real numbers pulled from the actual repo over generic textbook illustrations.
6. **Improvement ideas** — concrete findings about *programming practice* (not product or business calls), each paired with the specific failure scenario it would cause. Order by real impact, not by how interesting each one is to write about. Close by naming what the repo does well, too — a fair review isn't only a list of flaws.

### Style rules that matter

- Explain a term the reader hasn't confirmed knowing the first time it appears, anchored to something they already know ("same idea as X, which you know, but applied to Y").
- Put genuinely deep tangents (e.g. "how does this whole class of tooling work") in an appendix instead of interrupting the main narrative — but only add one if the main text actually needs it.
- A short glossary at the end helps once there are many new terms in play.
- Flag speculation as speculation; never present a guess as a verified fact.
- Only offer to publish the summary somewhere more visible/shared if that fits the sensitivity established in the calibration step.

---

## Phase 2 — Replica + incremental study plan

Start this only once the reader has read the summary and wants to go deeper hands-on.

### Set up an isolated sandbox

- A separate directory (e.g. `<project>_replica/`), never inside the reader's working branch of the original repo, and never touching the original repo's tracked files without the reader's explicit, separate approval.
- A real, isolated environment. Prefer a real virtual environment (stdlib `venv`, or `uv venv` / `uv sync` if a manifest exists) over conda: conda repeatedly causes friction in restricted/sandboxed environments (locked cache and env directories, solver failures), and — unlike a real venv — does not isolate the user's personal global site-packages by default, which silently leaks unrelated personal packages into `pip list`. Reach for conda only when there's a genuine non-Python system dependency that pip/uv truly can't provide.
- If the reader has their own reference material in an established pedagogical style (old course or project notebooks, a personal template), read a sample of it first and match that exact structure and tone — don't impose a generic template over one they've already built and trust.

### Build the study plan document, not the notebooks

Write a single `study_plan.md` inside the replica's notebooks directory. Its job is to sequence the learning, not to teach directly — the notebooks themselves teach, and the reader writes those.

**Ordering principle:** move from the most concrete/data-facing concept to the most abstract, then to how it's assembled into the whole system, then to business logic layered on top of the core algorithm's raw output, then to how it scales or deploys. Roughly: (1) data cleaning/representation → (2) the core algorithm, built from its smallest sub-piece up to the full thing → (3) business rules/post-processing on top of the algorithm's output → (4) orchestration/scaling/deployment → (5) anything not yet built, listed last, not skipped.

**Per-section template — but only for the section currently being worked on.** Use the headers in whichever output language was confirmed in the calibration step; both fixed variants are given below (do not mix the two, and do not invent a third phrasing). Each part is its own `###` subsection under the section's `##` heading — this is deliberate, not decoration: it keeps each part scannable on its own, gives the reader a proper outline/table-of-contents entry per part in tools that build one from headings, and (as a side benefit) sidesteps Markdown's fragile nested-list rendering entirely — nothing here needs to nest inside a bullet anymore, including "used in", which is now its own subsection rather than content tucked inside a reference-file bullet.

For each reference file that is a **module** (defines functions/classes consumed elsewhere, as opposed to a config file, a raw data file, or a standalone script nobody imports), fill in "Where they're used"/"Dónde se usan": search the repo for where each function/class is actually imported and called, and name each call site with a one-line note on what it's used *for* and roughly *where* in that caller's flow — don't skip the search and guess. If a section has more than one reference file, group this subsection by file using the file name in bold as a plain lead-in line (not a list item — see the template), never nested. Omit the whole subsection if none of the section's reference files are modules.

For each function or method the section is actually about (not every private helper in the file — just the ones the notebook will exercise), fill in "What each function is for"/"Qué resuelve cada función": a Google-style summary (purpose, `Args` with types, `Returns`/`Raises` with types) plus at least one worked example. **Run the example for real and paste its actual output — never compute it by hand.** String/regex transformations are exactly the kind of thing that looks obvious and is subtly wrong; a hand-guessed "expected" output that doesn't match the real function teaches the wrong lesson. Prefer an example that demonstrates *why the function exists* (e.g., two differently-formatted inputs that collapse to the identical output) over an arbitrary happy-path call. Close each function's block with a one-line "Used in"/"Se usa en" listing just the file names that call *this specific* function — reuse the same search already done for "Where they're used" above, filtered down to this one function, rather than searching again from scratch or guessing.

**Markdown formatting:** within a subsection, keep lists flat (no nesting) — a blank line before and after any list, no indentation deeper than a plain top-level bullet needs. Function names, `Args`/`Returns` bullets, and code examples are all plain top-level content under their `###` heading, never nested inside something else.

**Every file reference is a clickable relative link, not a plain code span** — `` [`<file>`](<relative-path>) `` rather than just `` `<file>` `` — so the reader can jump straight to the real file from an editor that resolves relative Markdown links (VSCode's preview and editor both do). Compute `<relative-path>` from `study_plan.md`'s own location to the actual file, e.g. if the plan lives at `<replica>/notebooks/study_plan.md` and the target is `<original-repo>/pkg/module.py` two directories up and back down, that's `../../<original-repo>/pkg/module.py` — verify it resolves (e.g. `ls` the computed path) before writing it, don't just eyeball the directory count. Keep the backticks *inside* the link text (`` [`file.py`](path) ``, not `` `[file.py](path)` ``) so it still reads as code.

English:

```markdown
## N. <topic>

### Reference files

- [`<file>`](<relative-path>) - <one line on what it demonstrates>
- [`<file_2>`](<relative-path>) - <one line on what it demonstrates>

### Where they're used

(omit this subsection if no reference file above is a module)

**[`<file>`](<relative-path>)**
1. [`<caller file>`](<relative-path>) - uses it for <what> in <where in that file's flow>
2. [`<caller file>`](<relative-path>) - uses it for <what> in <where in that file's flow>

### What each function is for

Each function signature is its own `####` heading — not an inline code span — so it's visually distinct from ordinary inline code references (like an argument name mentioned in a sentence) and shows up in an outline/table-of-contents view. Don't use HTML/CSS for this (e.g. a colored `<span>`): it's ignored by GitHub's sanitizer and inconsistent across renderers, whereas a heading is plain Markdown that renders the same everywhere. Letter each one (a, b, c…) when a section covers more than one function — this visually sets the "function purposes" list apart from the numbered "used in" list above it, which counts callers, not functions.

#### a) `function_name(arg1, arg2)`

Purpose: <what problem this function solves, in one or two sentences>.

- `arg1` (`<type>`): <what it is>
- `arg2` (`<type>`, default `<value>`): <what it is>

Returns `<type>`: <what comes back, including edge cases like None/empty>.

```python
function_name(<real example input>)
```
→ `<the real, executed output>`

Used in: [`<file>`](<relative-path>), [`<file_2>`](<relative-path>).

### Proposed notebook name

`0N-<slug>.ipynb`

### Supporting material

- [`<support file prepared for this section>`](<relative-path>) - <what it exemplifies>
- [`<support file prepared for this section>`](<relative-path>) - <what it exemplifies>
```

Español:

```markdown
## N. <tema>

### Archivos de referencia

- [`<archivo>`](<ruta-relativa>) - <una línea sobre qué demuestra>
- [`<archivo_2>`](<ruta-relativa>) - <una línea sobre qué demuestra>

### Dónde se usan

(omitir esta subsección si ningún archivo de referencia de arriba es un módulo)

**[`<archivo>`](<ruta-relativa>)**
1. [`<archivo que lo usa>`](<ruta-relativa>) - lo usa para <qué> en <dónde de su flujo>
2. [`<archivo que lo usa>`](<ruta-relativa>) - lo usa para <qué> en <dónde de su flujo>

### Qué resuelve cada función

#### a) `nombre_funcion(arg1, arg2)`

Propósito: <qué problema resuelve, en una o dos frases>.

- `arg1` (`<tipo>`): <qué es>
- `arg2` (`<tipo>`, default `<valor>`): <qué es>

Devuelve `<tipo>`: <qué regresa, incluidos los casos borde como None/vacío>.

```python
nombre_funcion(<input real de ejemplo>)
```
→ `<el output real, ya ejecutado>`

Se usa en: [`<archivo>`](<ruta-relativa>), [`<archivo_2>`](<ruta-relativa>).

### Propuesta de nombre del notebook

`0N-<slug>.ipynb`

### Material de apoyo

- [`<archivo de apoyo preparado para esta sección>`](<ruta-relativa>) - <qué ejemplifica>
- [`<archivo de apoyo preparado para esta sección>`](<ruta-relativa>) - <qué ejemplifica>
```

**Once the notebook for a section is actually finished** (the reader confirms it — see step 4 of the working loop below), retire the forward-looking placeholder: delete that section's `### Proposed notebook name`/`### Propuesta de nombre del notebook` subsection entirely, and add a new `### Development notebook`/`### Notebook de desarrollo` subsection in its place — but moved to the very *top* of the topic's block, right after the `## N. <topic>` heading and before `### Reference files`/`### Archivos de referencia`. It holds a clickable relative link to the real notebook file the reader wrote, using the same link convention as everywhere else in the plan:

```markdown
### Development notebook

[`01-normalization.ipynb`](01-normalization.ipynb)
```

(Spanish: `### Notebook de desarrollo`, same content otherwise.) The real filename may differ from the proposed slug — use whatever the reader actually named it. This turns each finished topic's block from a forward-looking checklist into a running index of what was actually built, with a working link straight to it, visible before anything else in that section.

**Every other section gets only:**

```markdown
## N. <topic>
<one-line summary of what this section will cover>
```

Nothing else for the inactive sections — no file list, no notebook name, no support material — until the reader explicitly says they're ready to move on to that one. Do not pre-fill several sections at once even if it would save a round-trip; the pacing is the point, not an inefficiency to optimize away.

### The working loop, once the plan exists

1. For the active section only, prepare **supporting material** — fixtures, small data samples, deliberately chosen edge cases — ideally pulled from real data already in the repo rather than invented from scratch (real messy data teaches better and surfaces genuine gotchas a clean synthetic example would hide). Never write the notebook itself, and never write example or solution code inside it — the reader builds the notebook, its code, and its explanation.
2. Point the reader toward this notebook shape once (referencing their own reference notebooks if they have some, matching that style) rather than reproducing it yourself each time: a markdown cell stating the concept/math *before* any code; the real function or class with its full docstring; an immediate small-scale demo printing shapes or outputs; build from the smallest sub-piece up to the composed whole; a cross-check (e.g. an `assert`) between a manual calculation and the real code's output.
3. Stop, and wait for the reader to say they've finished that notebook.
4. Once they confirm it's finished, replace that section's `### Proposed notebook name`/`### Propuesta de nombre del notebook` subsection with a `### Development notebook`/`### Notebook de desarrollo` subsection at the top of the block, linking to the real notebook file (see the note above the templates).
5. Only then fill in the next section's full template (files, notebook name, support material) and repeat.

### Track improvement ideas as they surface, in their own document

Studying real code closely enough to write a notebook about it surfaces things the upfront `repo_summary.md` pass never catches — often only found by testing a real alternative once you're deep enough in one function to try (e.g. discovering a well-established library does what a hand-rolled heuristic attempts, but more robustly, and proving it with a real comparison rather than asserting it). Capture these in a second living document, `improvement_ideas.md`, in the same directory as `study_plan.md` — separate from `repo_summary.md`'s own "Improvement ideas" section, which is a one-time broad survey done during the initial read-through; this one accumulates gradually, only as real findings surface while working through the plan.

Structure it with one `##` heading per `study_plan.md` topic, using the exact same numbers and titles, so the two documents cross-reference cleanly. Only add a section once it actually has a finding in it — don't pre-populate empty sections for topics not yet reached, same reasoning as the study plan's own inactive-section rule.

For each idea, give the reader real decision-support, not a unilateral recommendation — this document exists so they can bring it to whoever owns the codebase (a manager, a tech lead) and discuss it, not so Claude decides for them. When the idea proposes swapping in a library or a new dependency, don't leave "adds a dependency" as a vague con — measure it: installed disk size of the package and its own dependency tree (excluding generated bytecode caches, which inflate the number and aren't part of what actually ships), and a real runtime benchmark (e.g. `timeit`) comparing the old and new approach on both the case the code was built for *and* the typical/common case, since a swap can look cheap on the rare case it targets while being disproportionately slower on the common one that dominates real usage. A vague "this might be slower/heavier" is not decision-support; a measured "9.5x slower on the rare case, 5.6x slower on the common one, ~1.8 MB total" is.

```markdown
## N. <topic, matching study_plan.md exactly>

### <short name for the idea>

Qué es: <one or two sentences>.

A favor:
- <concrete advantage>
- <concrete advantage>

En contra:
- <concrete tradeoff or cost>
- <concrete tradeoff or cost>

Evidencia: <the real comparison/demo that supports this — never a claim without one; link or reproduce the actual test run>.
```

### What never belongs in the replica

- The original repo's real production code, copied wholesale — the replica is built from understanding, not copy-paste.
- Anything that must stay confined to the original repo or organization: sensitive data, credentials, internal URLs, real personal data. Everything in the replica, including every fixture and example, must be safe to exist independently of the original repo's access controls.

---

## Hard rule for this skill file itself

This file is meant to be reused across many different, unrelated codebases. When updating it after using it somewhere, genericize any lesson learned before it lands here — never let a specific project's real names, business details, technology choices, people, internal tools, or findings leak into this file.
