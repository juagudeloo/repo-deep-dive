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

**Place the reader on a level scale**, so depth decisions later aren't a vague judgment call:

1. Everyday language only. Vague goals. Can't evaluate an explanation beyond "that makes sense."
2. Some domain terms, possibly imprecise. Catches obvious errors in an explanation; misses plausible-looking wrong ones.
3. Decomposes problems. Uses general analytical language (parameterize, decompose, normalize). Recognizes a wrong approach but relies on the summary for the correct one.
4. Specifies what and how at a technical level. Precise domain terms. Catches non-obvious errors. Proposes alternatives with justification.
5. Full command of the domain. Evaluates nuanced alternatives; distinguishes better from merely different.

Treat the reader's own self-description (role, years of experience, resume) as a prior, not a measurement — someone senior in general can genuinely sit at Level 2 on this specific repo's domain. Let their answers to the targeted questions above set the working level, not their job title. Re-check it per area of the repo rather than assuming one level covers the whole thing — a reader can be Level 4 on the language and Level 1 on the specific technique the repo implements (e.g. strong at the programming language, novice at the numerical method it's built around), the same way expertise rarely transfers evenly across an unfamiliar repo's different concerns.

**Ask how the reader best internalizes a new concept**: a precise definition, a worked example, an analogy to something they already know, contrast with a term they'd confuse it with, or just seeing it used in context. Use their answer to decide how every new term is introduced for the rest of the engagement — see "Style rules that matter" below, which applies it.

(The level scale and the internalization-method question above are adapted from the dialogic-bootstrapping teaching methodology in [SwRI-IDEA-Lab/vocal_prompt](https://github.com/SwRI-IDEA-Lab/vocal_prompt).)

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

- Explain a term the reader hasn't confirmed knowing the first time it appears, using whichever internalization method they named during calibration (see "Before starting: calibrate") — a precise definition, a worked example, an analogy anchored to something they already know ("same idea as X, which you know, but applied to Y"), a contrast against a term they'd confuse it with, or the term simply used correctly in context. Default to analogy-plus-context if the reader didn't express a preference.
- Put genuinely deep tangents (e.g. "how does this whole class of tooling work") in an appendix instead of interrupting the main narrative — but only add one if the main text actually needs it.
- A short glossary at the end helps once there are many new terms in play.
- Flag speculation as speculation; never present a guess as a verified fact.
- Only offer to publish the summary somewhere more visible/shared if that fits the sensitivity established in the calibration step.

---

## Phase 2 — Replica + incremental study plan

Start this only once the reader has read the summary and wants to go deeper hands-on.

### Set up an isolated sandbox

- A separate directory (e.g. `<project>_replica/`), never inside the reader's working branch of the original repo, and never touching the original repo's tracked files without the reader's explicit, separate approval.
- A `docs/` directory at the workspace root, sibling to the replica (not nested inside it, and not inside the replica's `notebooks/` folder) — this is where `study_plan.md` and `improvement_ideas.md` live (see below), kept separate from the notebooks themselves for organization. If the reader already has a `docs/` folder for other planning material, use that same one rather than creating a second.
- A real, isolated environment. Prefer a real virtual environment (stdlib `venv`, or `uv venv` / `uv sync` if a manifest exists) over conda: conda repeatedly causes friction in restricted/sandboxed environments (locked cache and env directories, solver failures), and — unlike a real venv — does not isolate the user's personal global site-packages by default, which silently leaks unrelated personal packages into `pip list`. Reach for conda only when there's a genuine non-Python system dependency that pip/uv truly can't provide.
- If the reader has their own reference material in an established pedagogical style (old course or project notebooks, a personal template), read a sample of it first and match that exact structure and tone — don't impose a generic template over one they've already built and trust.
- Name the fixtures/support-material directory `support_material/`, never `tests/`. Rename on sight if an earlier session already created a `tests/` directory for this purpose, updating every notebook import and every `study_plan.md`/`improvement_ideas.md` reference to match.

### Build the study plan document, not the notebooks

Write a single `study_plan.md` in the workspace-root `docs/` directory (see "Set up an isolated sandbox" above) — not inside the replica's notebooks directory. Its job is to sequence the learning, not to teach directly — the notebooks themselves teach, and the reader writes those.

**Ordering principle:** move from the most concrete/data-facing concept to the most abstract, then to how it's assembled into the whole system, then to business logic layered on top of the core algorithm's raw output, then to how it scales or deploys. Roughly: (1) data cleaning/representation → (2) the core algorithm, built from its smallest sub-piece up to the full thing → (3) business rules/post-processing on top of the algorithm's output → (4) orchestration/scaling/deployment → (5) anything not yet built, listed last, not skipped.

**Per-section template — but only for the section currently being worked on.** Use the headers in whichever output language was confirmed in the calibration step; both fixed variants are given below (do not mix the two, and do not invent a third phrasing). Each part is its own `###` subsection under the section's `##` heading — this is deliberate, not decoration: it keeps each part scannable on its own, gives the reader a proper outline/table-of-contents entry per part in tools that build one from headings, and (as a side benefit) sidesteps Markdown's fragile nested-list rendering entirely — nothing here needs to nest inside a bullet anymore, including "used in", which is now its own subsection rather than content tucked inside a reference-file bullet.

**Every active section opens with a short plain-language paragraph**, right after the `## N. <topic>` heading and before any subsection (including "Supporting material"/"Material de apoyo") — what this topic is about and why it matters in the pipeline as a whole, at the reader's calibrated level. For an inactive section, the existing one-line summary already serves this role; don't add a second, longer paragraph on top of it.

For each reference file that is a **module** (defines functions/classes consumed elsewhere, as opposed to a config file, a raw data file, or a standalone script nobody imports), fill in "Where they're used"/"Dónde se usan": search the repo for where each function/class is actually imported and called, and name each call site with a one-line note on what it's used *for* and roughly *where* in that caller's flow — don't skip the search and guess. If a section has more than one reference file, group this subsection by file using the file name in bold as a plain lead-in line (not a list item — see the template), never nested. Omit the whole subsection if none of the section's reference files are modules.

For each function or method the section is actually about (not every private helper in the file — just the ones the notebook will exercise), fill in "What each function is for"/"Qué resuelve cada función": a Google-style summary (purpose, `Args` with types, `Returns`/`Raises` with types) plus **one worked example per distinct behavior described in its Purpose** — not just one example overall. If the purpose paragraph mentions a base case, a special case, and a tie-breaking rule (e.g. "collapses duplicates, rejects conflicts, and lets a later value win a collision"), that's three examples, one per clause, not one example that only covers the first. Skipping the others isn't saving space, it's leaving the documented behavior unverified. **Run every example for real and paste its actual output — never compute it by hand.** String/regex transformations are exactly the kind of thing that looks obvious and is subtly wrong; a hand-guessed "expected" output that doesn't match the real function teaches the wrong lesson — this caught a real signature error once (a documented default that didn't actually exist on the function, only on its caller). Prefer an example that demonstrates *why the function exists* (e.g., two differently-formatted inputs that collapse to the identical output) over an arbitrary happy-path call.

**When the function being documented is an instance method (its real signature starts with `self`), write the worked example as if it were a free function whose first argument is an explicit stand-in for the instance** — named after what it represents (e.g. `client`, `parser`), never literally `self`, and never using bound-method call syntax (`instance.method(args)`). Call it like any other positional argument: `process_batch(client, items)`, not `client.process_batch(items)`. This matches how the reader is encouraged to prototype methods in their own notebook — as ordinary functions where the instance just happens to be the first parameter — and keeps every worked example runnable standalone, outside any class body. Apply this consistently to every method-shaped function from here on, not just when the reader points it out on one.

**Check the real signature before assuming a stand-in is needed at all** — a `@staticmethod` never takes `self` (or anything else standing in for it), so it needs no instance and no class reference in the example: define it in the notebook as an ordinary module-level function with the identical body, and call it by its bare name (`clear_cache()`, not `client.clear_cache()` or `ApiClient.clear_cache()`). A `@classmethod` takes `cls` instead of `self` and is called on the class, not an instance — its notebook stand-in is a class (the real one, or a minimal stand-in class if the real one can't be built standalone), never an instance variable. Getting this wrong reads as harmless extra ceremony, but it's actively misleading: it implies the method reads or depends on per-instance state that a `@staticmethod` by definition cannot have, which is exactly the kind of detail this notebook methodology exists to make visible, not obscure.

**Build the stand-in instance by calling the class's real constructor with plain demo values** — `client = ApiClient(base_url="https://example.test/api", timeout=5)`, not a bypass trick. Only skip the real constructor when it does something the example can't afford or doesn't need for the method being demonstrated (a network/disk load, production-only inputs the sandbox doesn't have) — and when that happens, say so explicitly in the text, so the reader knows it's a deliberate exception for that one class, not the default way to build an instance.

Before writing that build-line note, actually trace what the real constructor does — read its body, not just its signature — and list every file it reaches into as its own bullet under "Reference files"/"Archivos de referencia", even a file that never appears in the method being documented. A constructor commonly pulls in a config loader, a settings singleton, or another module the section's main file list wouldn't otherwise include, and skipping it there is exactly the kind of gap the reader has to go dig up on their own later. Concretely: if the class under study loads its own settings internally through some other module when the caller doesn't supply one explicitly, that other module is a reference file for this section too, with a one-line note on why it matters for building the stand-in instance — not just the file that defines the method being taught.

If that trace turns up a constructor that resolves paths off its own module's `__file__` (e.g. `Path(__file__).resolve().parents[N]`), running it unmodified in a notebook raises `NameError: name '__file__' is not defined` — notebook cells have no module-level `__file__`, even when every argument the constructor needs is otherwise supplied. This isn't a reason to bypass the real constructor; shim it instead, with a comment explaining why: `__file__ = str(Path.cwd() / "<matching/relative/path.py>")`. `Path(...).resolve()` never requires the path to actually exist, so a fake-but-plausible location is enough as long as nothing downstream tries to read from it.

**Every lettered item under "What each function is for" gets its own "You should build:" subsection**, placed right at the top of that item, listing everything that has to exist in the notebook's namespace before its example can run — not just the stand-in instance covered above, but every small supporting type, protocol/interface, or private helper the traced constructor or function body actually touches. The gap this catches: a constructor commonly type-hints or instantiates a small supporting class (a request/response shape, an interface the injected collaborator must satisfy, a module-level type alias, a private helper the main function calls) that was never its own section anywhere in the plan, because it's minor enough to overlook — but the reader still has to type it out before anything runs, and nothing else in the plan told them so. Distinguish two cases in the wording: something already covered by an earlier section (point back to it, e.g. "already built in topic N") versus something that only ever appears in this section's own reference files and was never its own topic (spell it out in full, with a short inline code snippet if it's small — this is the case most likely to be missed, since the reader has no earlier section to have caught it in). For items after the first that reuse what an earlier item in the same section already built, a one-line "nothing new — reuses `X`/`Y` from (a)" is enough; only call out the delta if that later item needs one additional thing.

**Order the lettered items themselves by dependency, not by narrative importance — leaf helpers first, the function that composes them last.** The top-level "Ordering principle" above sequences topics across the whole plan (concrete → abstract → business rules → orchestration); apply that same bottom-up logic one level down, within a single section's lettered items. Before assigning letters, trace what the section's flagship function actually calls, and what those calls call in turn, down to helpers with no further in-section dependencies — those leaves get the earliest letters, and the flagship function that ties everything together always gets the last letter, never the first. Putting the flagship function first and its dependencies after it (even with a correct "You should build" list pointing ahead to them) forces the reader to jump past unfinished sections to build each dependency, then come back — real, avoidable lost time, not a cosmetic ordering preference. Once a real notebook exists for the section, its actual cell order is the ground truth: if the plan's lettering disagrees with it, fix the plan, not the notebook.

**Every worked example calls the function on a named fixture from that section's supporting material, never an unlabeled inline literal** — the whole point of the example is that the reader runs the *identical* call against their *own* implementation and checks it produces the *identical* output, and that only works if they can see exactly which fixture (by name) feeds the call, not just a value that happens to look similar to something in the fixtures file. Concretely: `resolve_label(UNMATCHED_ROWS[0]["category"], label_lookup)`, not `resolve_label("some-value", label_lookup)` — even if the two are byte-identical, the first is traceable back to the supporting material and the second silently isn't, and a reader who overlooks that will run a call that looks the same but doesn't import from anywhere, unable to tell whether their own version matches by design or by coincidence. When a function needs a value built from an earlier function in the same section (e.g. a `label_lookup` dict, or a `classifier` instance), show that build line too, not just the final call — the example must be copy-pasteable end to end, not missing an implied setup step. If truly no fixture fits a given example (rare — most functions in scope should have one), a literal is acceptable but say so isn't the common case to reach for.

**Verify every worked example by running only the exact text about to be published, starting from a fresh namespace — never cumulatively inside one long verification session.** A verification script that builds up state across many checks (fixture A, then fixture B that reuses A, then a call that reuses both) will make a snippet "pass" even when the *published* snippet silently omits a build step, because the missing variable is still sitting in the verification script's own memory, left over from an earlier check — the gap only surfaces for whoever pastes the isolated snippet into their own, empty notebook cell. Paste the snippet into a clean interpreter (or a new cell with nothing carried over) before trusting it. The same discipline applies to a configuration/options-style object reused with different field values across several items in the same section: give each variant an explicit, distinguishing name (`options_with_x`, `options_without_x`) rather than reusing one bare generic name whose implied configuration silently changes between items — a reader who copies a later item's example gets whatever that name last held in their own notebook, not what the example's author had in mind when writing it.

Before writing a worked example — or a "You should build" note — that assumes a function accepts a particular argument (e.g. an injectable collaborator/stub), re-read that function's actual signature and body rather than assuming it from a similarly-shaped function elsewhere in the same file. Two functions that look like they'd support the same calling convention don't always both accept the same parameters — confirm it, don't infer it.

Close each function's block with two one-line lines: "Defined in"/"Se define en" naming the single reference file this function actually lives in — required whenever a section lists more than one reference file, since otherwise there's no way to tell which file to open for a given function; omit it when the section has only one reference file, where it would be redundant with the section heading above — followed by "Used in"/"Se usa en" listing just the file names that call *this specific* function, reusing the same search already done for "Where they're used" above, filtered down to this one function, rather than searching again from scratch or guessing.

**Markdown formatting:** within a subsection, keep lists flat (no nesting) — a blank line before and after any list, no indentation deeper than a plain top-level bullet needs. Function names, `Args`/`Returns` bullets, and code examples are all plain top-level content under their `###` heading, never nested inside something else.

**Every file reference is a clickable relative link, not a plain code span** — `` [`<file>`](<relative-path>) `` rather than just `` `<file>` `` — so the reader can jump straight to the real file from an editor that resolves relative Markdown links (VSCode's preview and editor both do). Compute `<relative-path>` from `study_plan.md`'s own location to the actual file, e.g. if the plan lives at `docs/study_plan.md` and the target is `<original-repo>/pkg/module.py`, one directory up and back down, that's `../<original-repo>/pkg/module.py`; a notebook at `<replica>/notebooks/01-foo.ipynb` is `../<replica>/notebooks/01-foo.ipynb` — verify it resolves (e.g. `ls` the computed path) before writing it, don't just eyeball the directory count. Keep the backticks *inside* the link text (`` [`file.py`](path) ``, not `` `[file.py](path)` ``) so it still reads as code.

English:

```markdown
## N. <topic>

<one short plain-language paragraph: what this topic is about and why it matters>

### Supporting material

- [`<support file prepared for this section>`](<relative-path>) - <what it exemplifies, and what to import from it>
- [`<support file prepared for this section>`](<relative-path>) - <what it exemplifies, and what to import from it>

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

**Purpose:** <what problem this function solves, in one or two sentences>.

- `arg1` (`<type>`): <what it is>
- `arg2` (`<type>`, default `<value>`): <what it is>

Returns `<type>`: <what comes back, including edge cases like None/empty>.

```python
function_name(<real example input>)
```
→ `<the real, executed output>`

Defined in: [`<file>`](<relative-path>).

Used in: [`<file>`](<relative-path>), [`<file_2>`](<relative-path>).
```

Español:

```markdown
## N. <tema>

<un párrafo corto en lenguaje llano: de qué trata este tema y por qué importa>

### Material de apoyo

- [`<archivo de apoyo preparado para esta sección>`](<ruta-relativa>) - <qué ejemplifica, y qué importar de ahí>
- [`<archivo de apoyo preparado para esta sección>`](<ruta-relativa>) - <qué ejemplifica, y qué importar de ahí>

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

**Propósito:** <qué problema resuelve, en una o dos frases>.

- `arg1` (`<tipo>`): <qué es>
- `arg2` (`<tipo>`, default `<valor>`): <qué es>

Devuelve `<tipo>`: <qué regresa, incluidos los casos borde como None/vacío>.

```python
nombre_funcion(<input real de ejemplo>)
```
→ `<el output real, ya ejecutado>`

Se define en: [`<archivo>`](<ruta-relativa>).

Se usa en: [`<archivo>`](<ruta-relativa>), [`<archivo_2>`](<ruta-relativa>).
```

### Notebooks group topics, not one-per-topic

A notebook is not "one per topic" — it's one per coherent arc that ends in a clear deliverable.
Several consecutive topics normally share a single notebook (e.g. "understand and replicate the
core algorithm" can span the sub-piece, the composed whole, and a cross-check against the real
code's output — all one notebook, one continuous narrative). A new notebook starts only when a
topic begins a genuinely different arc — a new deliverable, not just the next step of the same one
— even if it reuses objects or outputs from the previous notebook.

**When a topic becomes active** (step 5 of the working loop below), decide whether it continues the
current notebook or starts a new one. This is usually obvious from the topic itself (does it
culminate the arc already in progress, or start a new one), but when it's genuinely ambiguous, ask
the reader rather than guessing — they're the one who'll be writing the file. A topic that starts a
new notebook typically reuses state from the previous one; say so explicitly and let the reader
rebuild that state at the top of the new notebook (rerun the relevant cells, or load a saved/cached
artifact) — notebooks should not depend on cross-notebook Python state, only on files each one can
point to.

Maintain a single `## Notebooks` index near the top of `study_plan.md`, source of truth for names,
scope, and links — not repeated inside every topic's block:

```markdown
## Notebooks

| Notebook | Temas | Tema general | Estado |
|---|---|---|---|
| [`01-<slug>.ipynb`](<relative-path>) | 1–N | <one-line topic> | Terminado |
| `02-<slug>.ipynb` (propuesto) | N+1– | <one-line topic> | En curso |
```

Update this table's status and add the real link once the reader confirms a notebook is actually
finished — the real filename may differ from the proposed slug, use whatever the reader actually
named it. Where a topic starts a new notebook, add one line right under its `## N. <topic>` heading
marking the transition, so it's visible without checking the index:

```markdown
**Empieza notebook nuevo:** `0N-<slug>.ipynb` — <one line on why this is a different arc, not just
the next step of the previous one>.
```

Topics that continue the current notebook get no such line — silence means "same notebook as the
topic before it."

**Every other section gets only:**

```markdown
## N. <topic>
<one-line summary of what this section will cover>
```

Nothing else for the inactive sections — no file list, no notebook name, no support material — until the reader explicitly says they're ready to move on to that one. Do not pre-fill several sections at once even if it would save a round-trip; the pacing is the point, not an inefficiency to optimize away.

### The working loop, once the plan exists

1. For the active section only, prepare **supporting material** — fixtures, small data samples, deliberately chosen edge cases — ideally pulled from real data already in the repo rather than invented from scratch (real messy data teaches better and surfaces genuine gotchas a clean synthetic example would hide). Never write the notebook itself, and never write example or solution code inside it — the reader builds the notebook, its code, and its explanation.
2. Point the reader toward this notebook shape once (referencing their own reference notebooks if they have some, matching that style) rather than reproducing it yourself each time: a markdown cell stating the concept/math *before* any code; the real function or class with its full docstring; an immediate small-scale demo printing shapes or outputs; build from the smallest sub-piece up to the composed whole; a cross-check (e.g. an `assert`) between a manual calculation and the real code's output.
3. Stop, and wait for the reader to say they've finished that section (written its code, in whichever notebook is currently active).
4. If that section was the last one in its notebook's arc, update the `## Notebooks` index (status + real link to the file, see above) once the reader confirms the notebook itself is done, not just the section.
5. Only then fill in the next section's full template (files, support material) — deciding first whether it continues the current notebook or starts a new one (see "Notebooks group topics" above) — and repeat.

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

This file is meant to be reused across many different, unrelated codebases. When updating it after using it somewhere, genericize any lesson learned before it lands here — never let a specific project's real names, business details, technology choices, people, internal tools, or findings leak into this file. This applies to every part of an edit — the prose, the code identifiers, the sample values in an example, all of it — not just the explicit claims. Before considering an edit to this file done, reread the exact text you're about to write (or grep it) for any term specific to the codebase you were just working in — a function name, a column name, a domain concept, a sample value — and replace it with a generic placeholder if found. Writing the lesson in generic terms from the start is easier than sanitizing it after; don't draft it against the real names and plan to clean up later.
