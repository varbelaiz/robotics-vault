---
name: ingest
description: Ingest a source PDF from Raw/ into the wiki. Triggers when Valentino asks to ingerir, agregar, procesar, filear, or "meter al wiki" a source from Raw/ — phrases like "ingerí 05-intro_robo_proba", "ingerí Teóricas/03-locomocion", "agregá esta PDF al wiki", "procesá Tutorial 5", "metelo al wiki", "hacé el ingest de TP2". The skill walks the full six-phase workflow (read PDF → discuss takeaways → propose plan → wait approval → execute → summarize), generates concept pages with grounded citations, captures slide screenshots via the slide-screenshot skill, updates _Overview / Home / index / log, and produces a single commit per ingestion. Plan-mode-friendly: phases 1–3 are read-only research suitable for the plan window; phase 4 is the ExitPlanMode gate; phases 5–6 execute on approval.
---

# Ingest Skill

Canonical runbook for adding a `Raw/` source to the wiki of Robótica. This skill is the operational complement to the schema in `CLAUDE.md` — when in doubt about page templates, hard rules, or YAML frontmatter, consult `CLAUDE.md`. When in doubt about partitioning, screenshot density, citation style, or commit format, consult the **worked reference** at the bottom of this document.

## When to invoke this skill

Trigger when Valentino asks to **add a source from `Raw/` into the wiki**:

- *"ingerí Teóricas/05-intro_robo_proba"*
- *"ingerí 02-paradigmas"* (the skill resolves the full path)
- *"agregá esta PDF al wiki"*
- *"procesá Tutorial 5"*
- *"metelo al wiki"*
- *"hacé el ingest de TP2"*

Do NOT invoke this skill when:

- Valentino is asking conceptually without writing to the wiki (*"explicame X"*, *"qué dice MCL?"*). That's the **query** workflow in `CLAUDE.md`.
- He is editing an existing page (typos, expanding an existing section) without bringing new source material.
- He wants a health check on the wiki (*"hacé un lint"*, *"qué links están rotos?"*). That's the **lint** workflow in `CLAUDE.md`.
- He explicitly says *"no hagas un ingest"* / *"no escribas todavía"*.

## Phases at a glance

1. **Read** the PDF.
2. **Discuss takeaways** with Valentino.
3. **Propose change plan**.
4. **Wait for user approval**.
5. **Execute** the plan.
6. **Summarize** what was touched.

Phases 1–3 are research and proposal only — no writes — and map naturally to the plan window. `ExitPlanMode` is the gate at phase 4.

## Phase 1 — Read the PDF

The `Read` tool requires the `pages:` parameter for any PDF longer than 10 pages and **caps each request at 20 pages**. Discover the page count first, then read in chunks:

```bash
mdls -name kMDItemNumberOfPages "Raw/.../<filename>.pdf"
# or, if Poppler is installed:
pdfinfo "Raw/.../<filename>.pdf" | grep -i pages
```

Then `Read` the PDF with `pages: "1-20"`, `pages: "21-40"`, etc.

## Phase 2 — Discuss takeaways

Map the PDF for Valentino with:

- A `slides → topic` markdown table covering the full PDF.
- Which slides carry strong visual value (diagrams, key formulas, worked examples, sensor/robot photos).
- Which existing modules and concept pages the source touches, and which forward-references will resolve once it is ingested.
- What is particularly relevant for the parcial.

Keep it tight — Valentino uses this to decide partition and emphasis.

## Phase 3 — Propose change plan

State **before touching any file**, with exact paths and counts:

- New concept pages to create (full paths in `wiki/<module>/`).
- Existing pages to update (backlinks, prerequisites, comparison sections).
- `_Overview.md` updates for affected modules.
- `Home.md` status changes, `index.md` entries, `log.md` ingest entry.
- Screenshots planned: a count per page + the slide numbers each comes from.
- Draft commit message.

### Concept-page partitioning heuristic

Each concept page should be a node that *other modules will wikilink to*. If a concept will be referenced from a future module (e.g. *Matriz Definida Positiva* is a hub for Kalman covariances), it earns its own page.

Avoid both extremes:
- **Too coarse**: one giant page per lecture loses graph value; references can't target sub-concepts.
- **Too fine**: a separate page per slide turns the graph into noise; maintenance burden grows.

A 41-slide lecture typically yields **4–8 concept pages**.

## Phase 4 — Wait for user approval

Wait. Do not pre-emptively start writing.

In plan mode, this is the `ExitPlanMode` checkpoint — present the plan and let Valentino accept or reject.

## Phase 5 — Execute

Order matters because later steps reference earlier outputs:

1. **Generate all screenshots first** in a single bash call chained with `&&`. Use `pdftoppm -singlefile` per the **`slide-screenshot` skill** — that skill is the source of truth for the capture command, naming convention, recovery procedure, and image policy.
2. **Spot-check** 2–4 of the visually complex PNGs via `Read`. Reading every PNG of a large ingestion is overkill — `pdftoppm` is deterministic; if the spot-checks render correctly the rest will too.
3. **Write the new concept pages** in parallel (one `Write` per page). Use **template A** from `CLAUDE.md` (frontmatter, back-link, prerequisites, intuition + grounded sections, variants, fuentes). Every section header carries an inline citation: `*(Teóricas <PDF short name>, slides X–Y)*`.
4. **Overwrite the affected `_Overview.md`** files (parallel `Write`). Use **template B** from `CLAUDE.md`.
5. **Edit `Home.md`** status (Esqueleto → En progreso, or En progreso → Completo when applicable), **`index.md`** entries (one line per new page under the right module), **`log.md`** ingest entry — all in parallel via `Edit`.
6. **Stage + commit + push** as one commit per ingestion.

## Phase 6 — Summary

Report to Valentino:

- Source ingested (path + slide count).
- Concept pages created (paths).
- Screenshots captured (count + folder).
- Meta files updated.
- Commit hash + push status.
- Forward-references introduced (wikilinks deliberately broken until later ingestions resolve them).

## Special cases

### TP ingestion

When ingesting a TP enunciado from `Raw/TPs/`:

- Create the TP page (**template C** from `CLAUDE.md`) in `wiki/7. ROS2 y TPs/`.
- **Also update** the `## En ROS2 / En los TPs` section of **every concept page** the TP uses, adding a wikilink to the TP and a one-line note about how the concept appears in the TP.

### Apunte del compañero

The classmate's summary at `Raw/Apuntes/Resumen Parcial-...pdf` is **complementary**, not authoritative. Anything that appears there but **not** in the lecture PDFs goes inside a callout:

```markdown
> [!info] Complemento (apunte de compañero)
> (claim) — fuente: `Raw/Apuntes/Resumen Parcial-...pdf` pág. X.
```

Never mix complementary material with lecture-derived prose as if it were official.

### Batch ingestion

Multiple sources without per-source confirmation is allowed **only when Valentino explicitly asks** (*"ingerí los teóricos 02 al 05 todos juntos"*). Default is one source at a time.

### Source not in `Raw/`

If Valentino asks to ingest something that is not in `Raw/` (e.g. a paper, a video transcript, an article), **stop and ask** before fabricating a source. Options: (a) he adds the file to `Raw/` first; (b) treat it as an external complemento with explicit `> [!info] Complemento externo` callouts.

## Operational gotchas

- **Bash CWD persists across calls.** A `cd` in one bash call carries to the next. Either start each ingest-related bash with `cd /Users/varbelaiz/Obsidian/Robotica` or use absolute paths everywhere.
- **Filenames with spaces or non-ASCII chars** must be quoted in bash. `git add` of paths with spaces sometimes needs the path quoted too.
- **Forward-references are normal.** Wikilinks to pages that don't exist yet appear as broken in Obsidian and resolve automatically when the target page is created. The lint workflow surfaces them as a category, separate from genuinely broken refs.
- **Hard rule — grounding.** Every section in a concept page must have an inline citation to a specific slide in `Raw/`. Cero invención. If something isn't in the slides and Valentino wants it added, mark it as `> [!info] Complemento externo` with explicit external source, or ask first.
- **Single commit per ingestion.** Don't fragment into "screenshots commit" + "pages commit" + "meta commit". One ingestion = one atomic commit makes the history readable and easy to revert.

## Worked reference

The Module 1 ingestion of `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf` (commit `a92dcd4`) is a complete example to model after:

- **Source**: 41-slide lecture covering vectores → transformaciones homogéneas.
- **Output**: 6 concept pages in `wiki/1. Fundamentos/` (Vectores, Matrices, Matriz Definida Positiva, Matriz Jacobiana, Rotaciones, Transformaciones Homogéneas).
- **Screenshots**: 29 PNGs in `wiki/1. Fundamentos/Img/`, ~5 per page on average.
- **Meta updates**: `_Overview.md` (M1) overwritten with full narrative, `Home.md` status flipped Esqueleto → En progreso, `index.md` entries added under M1, `log.md` ingest entry appended.
- **Forward-refs**: `[[EKF]]`, `[[Filtro de Kalman]]`, `[[MCL - Filtro de Partículas]]` left as broken wikilinks for upcoming ingestions to resolve.
- **Commit**: single `a92dcd4` with a structured message documenting the partition rationale.

`git show a92dcd4 --stat` shows the full file inventory; `git show a92dcd4 -- "wiki/1. Fundamentos/Filtro de Kalman" 2>/dev/null || git show a92dcd4 -- "wiki/1. Fundamentos/Vectores.md"` shows the canonical concept-page shape.

## Why this skill exists

The ingestion workflow is the central operation of this wiki — it's what builds and maintains everything. Encoding it as a skill (rather than scattered prose in `CLAUDE.md`) means:

- Future Claude sessions see explicit trigger phrases and load the runbook on demand, even without prior session context.
- The runbook has room to be detailed without bloating `CLAUDE.md`.
- Plan-mode sessions get a structured workflow that maps naturally to the `ExitPlanMode` gate (phases 1–3 propose, 4 approves, 5–6 execute).
- Future workflow improvements (new PDF source types, additional output formats, deeper TP integration) live in one place and don't have to be threaded through multiple files.
