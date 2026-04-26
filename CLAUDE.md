# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with this repository.

## What this repository is

An **Obsidian vault** that functions as a personal knowledge base for a university **Robotics** course. It is structured as an **LLM Wiki** (per Karpathy's pattern): concept pages are incrementally built and maintained by Claude, grounded strictly in the source PDFs under `Raw/`.

It is **not a software project** — there is no build, lint, or test tooling. The deliverable is well-organized Markdown notes that render correctly inside Obsidian and can be studied from.

All wiki content is written in **Spanish (Rioplatense / academic Spanish)**. Preserve language and register when editing.

The full design rationale, alternatives considered and trade-offs are recorded in [`DESIGN.md`](DESIGN.md).

## Repository structure

```
Raw/                                  ← sources of truth, NEVER modified
  Diapositivas/Teoricas/              14 lecture PDFs (00 → 12)
  Diapositivas/Tutoriales/            8 ROS2 tutorial PDFs (T1 → T8)
  Apuntes/                            classmate's parcial summary
  TPs/                                3 TP enunciados

wiki/                                 ← LLM-owned content, fully managed by Claude
  1. Fundamentos/
  2. Locomoción/
  3. Sensores y Modelos/
  4. Robótica Probabilística/
  5. Filtros Bayesianos/
  6. Mapeo/
  7. ROS2 y TPs/
  Each module folder contains:
    _Overview.md                      ← module narrative + suggested study order
    <Concept>.md                      ← atomic concept pages
    Img/                              ← screenshots of slides

Home.md                               ← entry point + status table for all modules
index.md                              ← flat catalog of every wiki page
log.md                                ← append-only operations log
CLAUDE.md                             ← this file
DESIGN.md                             ← design rationale
.claude/skills/slide-screenshot/      ← skill for capturing slides as PNG
```

## Hard rules — non-negotiable

### 1. Grounding
**Every claim in a concept page must trace back to a specific slide in `Raw/`.** Zero invention. Each section header carries an inline citation to its source slides:

```markdown
## 2. Modelo y supuestos  *(Teóricas 10-filtro_de_kalman, págs. 4–7)*
```

If a useful fact isn't in `Raw/`:
- Either omit it.
- Or add it inside a callout `> [!info] Complemento externo` with explicit external source.
- When unsure, **ask the user before inventing**. Default to "not in slides → not written".

### 2. Wikilinks for intra-vault navigation
Always use `[[Target|alias]]`. Never markdown links between vault pages. External references use regular markdown links.

### 3. Never modify `Raw/`
Read-only. The PDFs there are the source of truth.

### 4. No empty-placeholder concept pages
Create a concept page only when there is grounded content for it. Empty placeholders pollute `index.md` and the graph view.

## Page templates

### A — Concept page (the 80% of the wiki)

```markdown
---
modulo: 5. Filtros Bayesianos
estado: completo            # esqueleto | en-progreso | completo
fuentes:
  - Raw/Diapositivas/Teoricas/10-filtro_de_kalman-3.pdf
  - Raw/Diapositivas/Tutoriales/Tutorial 8_ KF, EKF, UKF.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Filtros Bayesianos]] | [[Home|← Inicio]]

# Filtro de Kalman

> Estimador óptimo recursivo para sistemas lineales con ruido gaussiano.

## Prerequisitos
- [[Regla de Bayes]]
- [[Probabilidad - Repaso|Gaussiana multivariada]]
- [[Algebra Lineal|Matrices y operaciones]]
- [[Filtros Discretos]] — la versión "discreta" de la misma idea

## 1. Intuición  *(Teóricas 10-filtro_de_kalman, págs. 1–3)*
(prosa explicativa)

![[KF predict-update.png]]
*Ciclo predict/update, slide 9.*

## 2. Modelo y supuestos  *(Teóricas 10-..., págs. 4–7)*
- Modelo de transición: $x_t = A_t x_{t-1} + B_t u_t + \varepsilon_t$
- Modelo de observación: $z_t = C_t x_t + \delta_t$
- Ruidos gaussianos $\varepsilon \sim \mathcal{N}(0, R_t)$, $\delta \sim \mathcal{N}(0, Q_t)$

## 3. Algoritmo  *(Teóricas 10-..., págs. 8–11)*
### Predicción
$$\bar{\mu}_t = A_t \mu_{t-1} + B_t u_t$$
$$\bar{\Sigma}_t = A_t \Sigma_{t-1} A_t^\top + R_t$$
### Actualización
(...)

## 4. Derivación  *(Teóricas 10-..., págs. 12–18)*
(derivación matemática completa, paso a paso)

## 5. Variantes y conexiones
- [[EKF]] — extiende a sistemas no lineales (linealización por Jacobianos)
- [[UKF]] — alternativa sin linealización, usa sigma points
- Caso particular continuo de [[Filtros Discretos]]

## 6. En ROS2 / En los TPs
- Implementado en [[Tutorial 8 - KF, EKF, UKF]]
- Usado en [[TP3]] para localización del robot

## Fuentes
- `Raw/Diapositivas/Teoricas/10-filtro_de_kalman-3.pdf` — págs. 1–18
- `Raw/Diapositivas/Tutoriales/Tutorial 8_ KF, EKF, UKF.pdf` — págs. 1–12
- `Raw/Apuntes/Resumen Parcial- Principios de la Robótica Autónoma.pdf` — sección Kalman
```

Sections 4, 5, 6 are **optional** — omit them when empty. **Do not fabricate content to fill them.**

### B — Module `_Overview.md`

```markdown
> [[Home|← Inicio]]

# Módulo 5 — Filtros Bayesianos

> Mantener una distribución de probabilidad sobre el estado del robot
> y actualizarla con cada observación, vía Bayes.

## Por qué este módulo
(narrativa: qué problema resuelve, dónde encaja en la materia)

## Recorrido sugerido
1. [[Filtros Discretos]] — la idea base con estado discreto
2. [[Modelo de Movimiento (Velocidad)]]
3. [[Modelo de Sensor]]
4. [[MCL - Filtro de Partículas]]
5. [[Filtro de Kalman]]
6. [[EKF]] → [[UKF]]

## Conecta con
- ⬅️ [[4. Robótica Probabilística/_Overview|Módulo 4: Probabilidad y Bayes]] (prerequisito)
- ➡️ [[6. Mapeo/_Overview|Módulo 6: Mapeo]] (consumidor)

## Páginas en este módulo
(lista plana de los .md del módulo)
```

### C — TP page (in module 7)

```markdown
> [[_Overview|← ROS2 y TPs]]

# TP2 — (título descriptivo)

## Objetivo
(resumen del enunciado, no copia textual)

## Conceptos que necesitás
- [[Modelo de Movimiento (Velocidad)]]
- [[Filtros Discretos]]
- [[ROS2 - Conceptos Base]]
- [[Gazebo y rviz]]

## Pasos del enunciado
(lista resumida en orden)

## Decisiones de diseño / notas de implementación
(se llena cuando estés/estuviste resolviéndolo)

## Fuentes
- `Raw/TPs/Enunciado TP2.pdf`
```

### D — ROS2 tutorial page (in module 7)

Same as A, but with emphasis on **code blocks** (`bash`, `python`, `xml` for launch files) and direct mapping to which tutorial PDF it corresponds to. Each tutorial gets its own page.

## Frontmatter YAML

| Field | Required | Values |
|---|---|---|
| `modulo` | yes | `<N>. <Module name>` |
| `estado` | yes | `esqueleto` / `en-progreso` / `completo` |
| `fuentes` | yes | list of paths relative to vault root |
| `ultima_actualizacion` | yes | `YYYY-MM-DD` |

## Images and the `slide-screenshot` skill

- **Location**: all images live in `wiki/<module>/Img/` (one folder per module).
- **Capture mechanism**: the `slide-screenshot` skill at `.claude/skills/slide-screenshot/SKILL.md`.
- **Liberal policy**: 6–10 images per concept page, more if all add visual value. Capture **diagrams, key formulas as they appear in the slide, definitions, worked examples, sensor/robot photos**. Skip purely textual or decorative slides.
- **Naming**: descriptive noun phrase, no date, no slide number. Optional concept prefix to avoid collisions across modules: `KF - predict update.png`, `EKF - jacobiano.png`.
- **Embedding**: Obsidian wikilink syntax `![[name.png]]`, with caption in italics directly below.
  ```markdown
  ![[KF predict-update.png]]
  *Ciclo predict/update del Kalman, slide 9.*
  ```

## Workflows

### Ingestion (default: one source at a time)

The ingestion workflow has six phases. **Plan mode** is well-suited for it: phases 1–3 are pure research and proposal (no writes), `ExitPlanMode` is the user-approval gate, phases 5–6 execute after the user exits plan mode.

#### Phases at a glance

1. **Read** the PDF.
2. **Discuss takeaways** with the user.
3. **Propose change plan**.
4. **Wait for user approval**.
5. **Execute** in order: screenshots → concept pages → `_Overview.md` of affected modules → `Home.md` → `index.md` → `log.md` → commit + push.
6. **Summarize** what was touched (paths + commit hash).

#### Phase 1 — Reading the PDF

The `Read` tool requires the `pages:` parameter for any PDF longer than 10 pages, and **caps each request at 20 pages**. Discover the page count first, then read in chunks:

```bash
mdls -name kMDItemNumberOfPages "Raw/.../<filename>.pdf"
# or
pdfinfo "Raw/.../<filename>.pdf" | grep -i pages
```

Then `Read` with `pages: "1-20"`, `pages: "21-40"`, etc.

#### Phase 2 — Takeaways

Map the PDF for the user:
- A `slides → topic` table covering the full PDF.
- Which slides carry strong visual value (diagrams, key formulas, worked examples, sensor/robot photos).
- Which existing modules and concept pages the source touches, and which forward-references will resolve once it is ingested.

#### Phase 3 — Change plan

Propose **before touching any file**, with exact paths and counts:

- New concept pages to create.
- Existing pages to update (backlinks, prerequisites, comparison sections).
- `_Overview.md` updates for affected modules.
- `Home.md` status changes, `index.md` entries, `log.md` entry.
- Screenshots planned: count per page + the slide numbers each comes from.
- Draft commit message.

**Partitioning heuristic for concept pages**: each concept page should be a node that *other modules will wikilink to*. If a concept will be referenced from a future module, it earns its own page. Avoid both extremes — one giant page (loses graph value) and over-fragmenting into micro pages.

#### Phase 4 — User approval

Wait. Do not pre-emptively start writing. In plan mode, this is the `ExitPlanMode` checkpoint.

#### Phase 5 — Execution order

1. **Generate all screenshots first** in a single bash call (chained with `&&`). Use `pdftoppm -singlefile` (see `slide-screenshot/SKILL.md`).
2. **Spot-check** 2–4 of the visually complex PNGs via `Read`. Reading every PNG of a large ingestion is overkill — `pdftoppm` is deterministic.
3. **Write the new concept pages** in parallel (one `Write` per page).
4. **Overwrite the affected `_Overview.md`** files (parallel `Write`).
5. **Edit `Home.md` status, `index.md` entries, `log.md` ingest entry** in parallel.
6. **Stage + commit + push** as one commit per ingestion.

#### Phase 6 — Summary

Report:
- Source ingested (path + slide count).
- Concept pages created (paths).
- Screenshots captured (count + folder).
- Meta files updated.
- Commit hash + push status.
- Forward-references introduced (wikilinks deliberately broken until later ingestions).

#### Special cases

- **TP ingestion**: also update the `## En ROS2 / En los TPs` section of every concept page the TP uses.
- **Apunte del compañero**: treat as a complementary source. Anything not in the lecture PDFs goes inside `> [!info] Complemento (apunte de compañero)` callouts, never mixed in as if it were official material.
- **Batch ingestion** (multiple sources without per-source confirmation) is allowed when the user explicitly requests it.

#### Operational gotchas

- **Bash CWD persists across calls.** A `cd` in one bash call carries to the next. Either start each ingest-related bash with `cd /Users/varbelaiz/Obsidian/Robotica` or use absolute paths everywhere.
- **Filenames with spaces or non-ASCII chars** must be quoted in bash. `git add` of paths with spaces sometimes needs the path quoted too.
- **Forward-references are normal.** Wikilinks to pages that don't exist yet appear as broken in Obsidian and resolve automatically when the target page is created. The `lint` workflow surfaces them as a category, separate from genuinely broken refs.

#### Worked reference

The Module 1 ingestion (`Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf`, commit `a92dcd4`) is a complete worked example: 6 concept pages from a 41-slide source, 29 screenshots, single commit with structured message, deliberate forward-refs to upcoming filter pages. Use it as a template when in doubt about partitioning, screenshot density, or commit format.

### Query

1. **Read `index.md`** first to locate relevant pages.
2. **Read those pages**, drill down via wikilinks if needed.
3. **Answer in chat** with wikilinks and slide citations.
4. **Offer to file the answer back** to the wiki when valuable: as a new section in an existing page, a new page, or a comparison table.
5. If the answer requires content **outside `Raw/`**, flag it explicitly and ask: leave it in chat only, search externally and mark as complemento, or skip.
6. **Append to `log.md`**.

### Lint (on demand)

Check for:
- Orphan pages (no inbound `[[links]]`).
- Broken wikilinks (target `.md` doesn't exist).
- Concepts mentioned across pages without their own `.md`.
- Sections without a grounding citation.
- Citations to slide pages that don't exist in the source PDF.
- Orphan images in `Img/` (not referenced from any `.md`).
- Status mismatches between page YAML `estado` and `Home.md`'s table.
- Inconsistent frontmatter (missing fields, malformed dates).
- Contradictions between pages.
- Proactive suggestions: high-traffic concepts that would benefit from their own page; data gaps that an external source could fill.

Report findings grouped by **severity**: breaks-the-wiki / inconsistency / improvement.

## `log.md` format

Append-only, one entry per operation, parseable prefix:

```markdown
## [YYYY-MM-DD] {ingest|consulta|lint} | <short title>
- (bullets describing what was done)
```

Recent activity: `grep "^## \[" log.md | tail -10`.

## `index.md` format

Catalog grouped by module. One line per page:

```markdown
- [[Filtro de Kalman]] — Estimador óptimo recursivo para sistemas lineales con ruido gaussiano.
```

Updated on every ingestion.

## Misc conventions

- **LaTeX** for math: `$...$` inline, `$$...$$` block. Obsidian renders natively.
- **Dates** everywhere: `YYYY-MM-DD`.
- **Back-link header** mandatory on every page except `Home.md`: `> [[_Overview|← <Module>]] | [[Home|← Inicio]]`.
- **Aliases** in wikilinks when prose flow benefits: `[[Regla de Bayes|Bayes]]`.
- **Don't reformat existing notes wholesale.** The voice and structure are deliberate. Make minimal, targeted edits unless asked for a rewrite.
- **Image folder is per-module** (`wiki/<module>/Img/`), not vault-wide, to keep the graph clean and avoid collisions.

## Path translation

When working through Claude's file tools the path is `/Users/varbelaiz/Obsidian/Robotica/...`; in the bash sandbox the same folder is `/sessions/<session>/mnt/Robotica/...`. Use the bash mount path for shell commands (e.g. `pdftoppm` calls inside the `slide-screenshot` skill).
