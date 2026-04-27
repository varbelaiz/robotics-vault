# Bootstrap Wiki de Robótica — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Dejar el vault de Robótica listo para la primera ingesta. Esto incluye: meta files (`Home`, `index`, `log`), esqueletos de los 7 módulos, eliminación del `Welcome.md` placeholder de Obsidian, y adaptación de la skill `slide-screenshot` desde el patrón NLP al patrón Robótica.

**Architecture:** Tres fases (meta files + cleanup → módulos → skill), tres commits, push final al remoto. Ninguna fase ingiere fuentes todavía — el plan termina cuando el vault está listo para que se dispare la primera ingesta de `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf` siguiendo el workflow descrito en `CLAUDE.md`. (`00-introduccion` no se ingiere — el usuario indicó que no entra en el parcial y no lo va a estudiar.)

**Tech Stack:** Markdown · Obsidian · git · `pdftoppm` (Poppler, ya disponible en macOS si está instalado Homebrew Poppler) · `gh` CLI.

---

## Premisas

- Vault root: `/Users/varbelaiz/Obsidian/Robotica/`.
- En el bash sandbox la ruta es `/sessions/<session>/mnt/Robotica/`. **Usar la ruta del sandbox para comandos shell, la ruta `/Users/...` para tools de archivo (Read/Write/Edit).**
- `git`, `gh` ya configurados (cuenta `varbelaiz`, repo privado `varbelaiz/robotics-vault` ya creado y trackeando `origin/main`).
- DESIGN.md y CLAUDE.md ya están commiteados; este plan los aplica.
- Las páginas conceptuales **no se crean en este plan** — sólo esqueletos de módulo. Las páginas conceptuales reales nacen en la primera ingesta y respetan la regla de grounding.

## Mapa de archivos a crear / modificar / eliminar

| Acción | Path | Responsabilidad |
|---|---|---|
| Create | `index.md` | Entry point del wiki, tabla de estado de los 7 módulos, navegación |
| Create | `index.md` | Catálogo plano de páginas, agrupado por módulo |
| Create | `log.md` | Bitácora append-only |
| Delete | `Welcome.md` | Placeholder default de Obsidian, reemplazado por `index.md` |
| Create | `wiki/1. Fundamentos/_Overview.md` | Hub local del módulo 1 |
| Create | `wiki/1. Fundamentos/Img/.gitkeep` | Mantener carpeta de imágenes vacía bajo git |
| Create | `wiki/2. Locomoción/_Overview.md` | Hub local del módulo 2 |
| Create | `wiki/2. Locomoción/Img/.gitkeep` | idem |
| Create | `wiki/3. Sensores y Modelos/_Overview.md` | Hub local del módulo 3 |
| Create | `wiki/3. Sensores y Modelos/Img/.gitkeep` | idem |
| Create | `wiki/4. Robótica Probabilística/_Overview.md` | Hub local del módulo 4 |
| Create | `wiki/4. Robótica Probabilística/Img/.gitkeep` | idem |
| Create | `wiki/5. Filtros Bayesianos/_Overview.md` | Hub local del módulo 5 |
| Create | `wiki/5. Filtros Bayesianos/Img/.gitkeep` | idem |
| Create | `wiki/6. Mapeo/_Overview.md` | Hub local del módulo 6 |
| Create | `wiki/6. Mapeo/Img/.gitkeep` | idem |
| Create | `wiki/7. ROS2 y TPs/_Overview.md` | Hub local del módulo 7 |
| Create | `wiki/7. ROS2 y TPs/Img/.gitkeep` | idem |
| Edit | `.claude/skills/slide-screenshot/SKILL.md` | Adaptar trigger, paths, cap, fuentes a Robótica |

---

## Phase 1 — Meta files + cleanup

### Task 1: Create `index.md`

**Files:** Create `index.md`

- [ ] **Step 1: Write the file**

```markdown
# Wiki de Robótica

> Knowledge base de la materia universitaria de **Robótica** (estilo *LLM Wiki*).
> Schema operativo: [[CLAUDE]]. Diseño y rationale: [[DESIGN]].

---

## Módulos

| # | Módulo | Estado |
|---|---|---|
| 1 | [[1. Fundamentos/_Overview\|Fundamentos]] | Esqueleto |
| 2 | [[2. Locomoción/_Overview\|Locomoción]] | Esqueleto |
| 3 | [[3. Sensores y Modelos/_Overview\|Sensores y Modelos]] | Esqueleto |
| 4 | [[4. Robótica Probabilística/_Overview\|Robótica Probabilística]] | Esqueleto |
| 5 | [[5. Filtros Bayesianos/_Overview\|Filtros Bayesianos]] | Esqueleto |
| 6 | [[6. Mapeo/_Overview\|Mapeo]] | Esqueleto |
| 7 | [[7. ROS2 y TPs/_Overview\|ROS2 y TPs]] | Esqueleto |

> Estados posibles: `Esqueleto` (sin contenido grounded) · `En progreso` (alguna ingesta hecha) · `Completo` (todas las fuentes del módulo ingeridas).

## Navegación rápida

- [[Robotica|Catálogo plano de todas las páginas]]
- [[log|Bitácora del wiki]]

## Fuentes en `Raw/`

- `Raw/Diapositivas/Teoricas/` — 14 PDFs teóricos numerados 00 → 12.
- `Raw/Diapositivas/Tutoriales/` — 8 tutoriales de ROS2 (T1 → T8).
- `Raw/Apuntes/` — Resumen del compañero (excluido del repo por tamaño, sólo local).
- `Raw/TPs/` — Enunciados TP1, TP2, TP3.
```

### Task 2: Create `index.md`

**Files:** Create `index.md`

- [ ] **Step 1: Write the file**

```markdown
# Catálogo del Wiki

> Lista plana de todas las páginas del wiki, agrupadas por módulo.
> Actualizado en cada ingesta. Una línea por página: `[[link]] — descripción corta.`

> [[Home|← Inicio]]

---

## 1. Fundamentos
- [[1. Fundamentos/_Overview|_Overview]] — *(módulo en esqueleto, pendiente de ingesta)*

## 2. Locomoción
- [[2. Locomoción/_Overview|_Overview]] — *(módulo en esqueleto, pendiente de ingesta)*

## 3. Sensores y Modelos
- [[3. Sensores y Modelos/_Overview|_Overview]] — *(módulo en esqueleto, pendiente de ingesta)*

## 4. Robótica Probabilística
- [[4. Robótica Probabilística/_Overview|_Overview]] — *(módulo en esqueleto, pendiente de ingesta)*

## 5. Filtros Bayesianos
- [[5. Filtros Bayesianos/_Overview|_Overview]] — *(módulo en esqueleto, pendiente de ingesta)*

## 6. Mapeo
- [[6. Mapeo/_Overview|_Overview]] — *(módulo en esqueleto, pendiente de ingesta)*

## 7. ROS2 y TPs
- [[7. ROS2 y TPs/_Overview|_Overview]] — *(módulo en esqueleto, pendiente de ingesta)*
```

### Task 3: Create `log.md`

**Files:** Create `log.md`

- [ ] **Step 1: Write the file**

```markdown
# Bitácora del Wiki

> Append-only. Una entrada por operación.
> Formato: `## [YYYY-MM-DD] {ingest|consulta|lint|bootstrap} | <título corto>`
> Recientes: `grep "^## \[" log.md | tail -10`

> [[Home|← Inicio]]

---

## [2026-04-26] bootstrap | inicialización del wiki
- Creados meta files: `index.md`, `index.md`, `log.md`
- Creados esqueletos de los 7 módulos (`_Overview.md` + `Img/`)
- Skill `slide-screenshot` adaptada de NLP a Robótica
- Eliminado `Welcome.md` placeholder default de Obsidian
- Repo: `varbelaiz/robotics-vault` (privado), trackea `origin/main`
- Próximo paso: primera ingesta de `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf` (la 00-introduccion se omite — no entra en el parcial)
```

### Task 4: Delete `Welcome.md`

**Files:** Delete `Welcome.md`

- [ ] **Step 1: Untrack and remove from disk**

Run in vault root:
```bash
git rm Welcome.md
```

Expected: `rm 'Welcome.md'` printed; file removed from disk and staged for deletion.

### Task 5: Commit Phase 1

**Files:** No new files; commits the changes from Tasks 1–4.

- [ ] **Step 1: Stage and verify**

```bash
git add index.md index.md log.md
git status --short
```

Expected:
```
A  index.md
A  index.md
A  log.md
D  Welcome.md
```

- [ ] **Step 2: Commit**

```bash
git commit -m "$(cat <<'EOF'
Bootstrap meta files and remove Obsidian placeholder

- index.md: entry point with module status table and quick navigation
- index.md: flat catalog of pages, grouped by module
- log.md: append-only operations bitácora with bootstrap entry
- Welcome.md: removed (replaced by index.md as the entry point)

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
EOF
)"
```

Expected: commit created on `main`.

---

## Phase 2 — Module skeletons

### Task 6: Create the 7 module folders with `_Overview.md` and `Img/.gitkeep`

**Files:**
- Create `wiki/1. Fundamentos/_Overview.md` and `wiki/1. Fundamentos/Img/.gitkeep`
- Create `wiki/2. Locomoción/_Overview.md` and `wiki/2. Locomoción/Img/.gitkeep`
- Create `wiki/3. Sensores y Modelos/_Overview.md` and `wiki/3. Sensores y Modelos/Img/.gitkeep`
- Create `wiki/4. Robótica Probabilística/_Overview.md` and `wiki/4. Robótica Probabilística/Img/.gitkeep`
- Create `wiki/5. Filtros Bayesianos/_Overview.md` and `wiki/5. Filtros Bayesianos/Img/.gitkeep`
- Create `wiki/6. Mapeo/_Overview.md` and `wiki/6. Mapeo/Img/.gitkeep`
- Create `wiki/7. ROS2 y TPs/_Overview.md` and `wiki/7. ROS2 y TPs/Img/.gitkeep`

**Note:** Each `_Overview.md` is intentionally a **skeleton with placeholders**, *not* prose written from inference. Per the grounding rule in `CLAUDE.md`, narrative content fills in only when the corresponding source PDFs are ingested.

The `.gitkeep` files exist solely to track empty `Img/` folders under git (git ignores empty directories).

- [ ] **Step 1: Write `wiki/1. Fundamentos/_Overview.md`**

```markdown
> [[Home|← Inicio]]

# Módulo 1 — Fundamentos

> *(tagline pendiente — se completa con la primera ingesta del módulo)*

## Por qué este módulo
*(narrativa pendiente)*

## Recorrido sugerido
*(orden de páginas pendiente)*

## Conecta con
- ➡️ Es prerequisito de todos los módulos posteriores.

## Páginas en este módulo
*(vacío — se llena al ingerir fuentes)*
```

- [ ] **Step 2: Write `wiki/1. Fundamentos/Img/.gitkeep`**

Empty file (zero bytes).

- [ ] **Step 3: Write `wiki/2. Locomoción/_Overview.md`**

```markdown
> [[Home|← Inicio]]

# Módulo 2 — Locomoción

> *(tagline pendiente — se completa con la primera ingesta del módulo)*

## Por qué este módulo
*(narrativa pendiente)*

## Recorrido sugerido
*(orden de páginas pendiente)*

## Conecta con
- ⬅️ [[1. Fundamentos/_Overview|Módulo 1: Fundamentos]] (matemática base)
- ➡️ [[5. Filtros Bayesianos/_Overview|Módulo 5: Filtros Bayesianos]] (los modelos de movimiento alimentan los filtros)

## Páginas en este módulo
*(vacío — se llena al ingerir fuentes)*
```

- [ ] **Step 4: Write `wiki/2. Locomoción/Img/.gitkeep`**

Empty file.

- [ ] **Step 5: Write `wiki/3. Sensores y Modelos/_Overview.md`**

```markdown
> [[Home|← Inicio]]

# Módulo 3 — Sensores y Modelos

> *(tagline pendiente — se completa con la primera ingesta del módulo)*

## Por qué este módulo
*(narrativa pendiente)*

## Recorrido sugerido
*(orden de páginas pendiente)*

## Conecta con
- ⬅️ [[1. Fundamentos/_Overview|Módulo 1: Fundamentos]]
- ⬅️ [[4. Robótica Probabilística/_Overview|Módulo 4: Robótica Probabilística]] (los modelos de sensor son probabilísticos)
- ➡️ [[5. Filtros Bayesianos/_Overview|Módulo 5: Filtros Bayesianos]] (los filtros consumen modelos de sensor)

## Páginas en este módulo
*(vacío — se llena al ingerir fuentes)*
```

- [ ] **Step 6: Write `wiki/3. Sensores y Modelos/Img/.gitkeep`**

Empty file.

- [ ] **Step 7: Write `wiki/4. Robótica Probabilística/_Overview.md`**

```markdown
> [[Home|← Inicio]]

# Módulo 4 — Robótica Probabilística

> *(tagline pendiente — se completa con la primera ingesta del módulo)*

## Por qué este módulo
*(narrativa pendiente)*

## Recorrido sugerido
*(orden de páginas pendiente)*

## Conecta con
- ⬅️ [[1. Fundamentos/_Overview|Módulo 1: Fundamentos]]
- ➡️ [[5. Filtros Bayesianos/_Overview|Módulo 5: Filtros Bayesianos]] (Bayes alimenta todos los filtros)
- ➡️ [[6. Mapeo/_Overview|Módulo 6: Mapeo]]

## Páginas en este módulo
*(vacío — se llena al ingerir fuentes)*
```

- [ ] **Step 8: Write `wiki/4. Robótica Probabilística/Img/.gitkeep`**

Empty file.

- [ ] **Step 9: Write `wiki/5. Filtros Bayesianos/_Overview.md`**

```markdown
> [[Home|← Inicio]]

# Módulo 5 — Filtros Bayesianos

> *(tagline pendiente — se completa con la primera ingesta del módulo)*

## Por qué este módulo
*(narrativa pendiente)*

## Recorrido sugerido
*(orden de páginas pendiente)*

## Conecta con
- ⬅️ [[1. Fundamentos/_Overview|Módulo 1: Fundamentos]] (álgebra para Kalman)
- ⬅️ [[2. Locomoción/_Overview|Módulo 2: Locomoción]] (modelos de movimiento)
- ⬅️ [[3. Sensores y Modelos/_Overview|Módulo 3: Sensores]] (modelos de observación)
- ⬅️ [[4. Robótica Probabilística/_Overview|Módulo 4: Probabilidad]] (Bayes)
- ➡️ [[6. Mapeo/_Overview|Módulo 6: Mapeo]] (los filtros se usan ahí)

## Páginas en este módulo
*(vacío — se llena al ingerir fuentes)*
```

- [ ] **Step 10: Write `wiki/5. Filtros Bayesianos/Img/.gitkeep`**

Empty file.

- [ ] **Step 11: Write `wiki/6. Mapeo/_Overview.md`**

```markdown
> [[Home|← Inicio]]

# Módulo 6 — Mapeo

> *(tagline pendiente — se completa con la primera ingesta del módulo)*

## Por qué este módulo
*(narrativa pendiente)*

## Recorrido sugerido
*(orden de páginas pendiente)*

## Conecta con
- ⬅️ [[4. Robótica Probabilística/_Overview|Módulo 4: Probabilidad]]
- ⬅️ [[5. Filtros Bayesianos/_Overview|Módulo 5: Filtros Bayesianos]]

## Páginas en este módulo
*(vacío — se llena al ingerir fuentes)*
```

- [ ] **Step 12: Write `wiki/6. Mapeo/Img/.gitkeep`**

Empty file.

- [ ] **Step 13: Write `wiki/7. ROS2 y TPs/_Overview.md`**

```markdown
> [[Home|← Inicio]]

# Módulo 7 — ROS2 y TPs

> *(tagline pendiente — se completa con la primera ingesta del módulo)*

## Por qué este módulo
*(narrativa pendiente)*

## Recorrido sugerido
*(orden de páginas pendiente)*

## Conecta con
- ⬅️ Todos los módulos teóricos (M1–M6) — los TPs y tutoriales aterrizan los conceptos en código.

## Páginas en este módulo
*(vacío — se llena al ingerir fuentes)*

## TPs
- *(página pendiente)* TP1
- *(página pendiente)* TP2
- *(página pendiente)* TP3
```

- [ ] **Step 14: Write `wiki/7. ROS2 y TPs/Img/.gitkeep`**

Empty file.

### Task 7: Commit Phase 2

- [ ] **Step 1: Stage and verify**

```bash
git add wiki/
git status --short
```

Expected: 14 files added (7 `_Overview.md` + 7 `.gitkeep`).

- [ ] **Step 2: Commit**

```bash
git commit -m "$(cat <<'EOF'
Add module skeletons for the 7 wiki modules

Each module gets:
- _Overview.md with title, back-link, and placeholder sections.
  Narrative content (tagline, "Por qué", recorrido, páginas) intentionally
  empty until the first source for the module is ingested, per the grounding
  rule in CLAUDE.md.
- Img/.gitkeep so the empty image folders are tracked.

Modules: Fundamentos, Locomoción, Sensores y Modelos, Robótica Probabilística,
Filtros Bayesianos, Mapeo, ROS2 y TPs.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
EOF
)"
```

---

## Phase 3 — Adapt the `slide-screenshot` skill

The skill copied from NLP (`/Users/varbelaiz/Obsidian/Robotica/.claude/skills/slide-screenshot/SKILL.md`) is hard-wired to NLP conventions:

| NLP convention | Robótica equivalent |
|---|---|
| Trigger: writing answers to `Preguntas.md` | Trigger: writing/expanding wiki concept pages OR ingesting a new source |
| One PDF per class folder, named `CLASE N -...pdf` | Multiple sources in `Raw/{Diapositivas/{Teoricas,Tutoriales},Apuntes,TPs}/` |
| Output folder: `<class folder>/Imagenes/` | Output folder: `wiki/<module>/Img/` |
| Cap: 3 images per answer | Cap: 6–10 per page (more if all add value) |
| Capture only diagrams / architectures | Also: key formulas as they appear in slides, definitions, worked examples, sensor/robot photos |
| Path: `/Users/varbelaiz/Obsidian/NLP/...` ↔ `/sessions/<session>/mnt/NLP/...` | Path: `/Users/varbelaiz/Obsidian/Robotica/...` ↔ `/sessions/<session>/mnt/Robotica/...` |

The mechanical guarantees stay intact: pre-render verification with `Read pages:`, post-render `Read` of the PNG, the recovery procedure for orphans, and the descriptive-naming convention.

### Task 8: Rewrite `slide-screenshot/SKILL.md`

**Files:** Modify `.claude/skills/slide-screenshot/SKILL.md`

The cleanest path is a **full overwrite** with the adapted content (the structure stays but most prose changes). We will use `Write` to overwrite, after `Read`-ing the current file once for the harness's tracking.

- [ ] **Step 1: Read the current file once** (required by the Write tool before overwrite)

Use the `Read` tool on `.claude/skills/slide-screenshot/SKILL.md`.

- [ ] **Step 2: Overwrite with the Robótica-adapted content**

Use `Write` on `.claude/skills/slide-screenshot/SKILL.md` with this full content:

````markdown
---
name: slide-screenshot
description: Captures relevant slide visuals (diagrams, formulas, definitions, worked examples) from PDFs in `Raw/` and embeds them inline when writing or expanding wiki concept pages in the Robótica vault. Use this skill whenever Valentino is ingesting a new source from `Raw/` into the wiki, or expanding/rewriting a concept page in `wiki/<module>/`. The skill handles the full mechanical loop — identifying which slide pages contain visuals worth showing, extracting them as PNG with `pdftoppm`, naming them per vault convention, and inserting them with Obsidian wikilink syntax + italic caption. Triggers automatically as part of any wiki write — Valentino does not need to ask for screenshots explicitly.
---

# Slide Screenshot Skill

Captures slide images from PDFs in `Raw/` and embeds them in the wiki page being written under `wiki/<module>/`, with the captured PNG stored in that module's `Img/` folder.

## When to invoke this skill

Trigger this skill when Valentino is:

- **Ingesting a new source** from `Raw/` into the wiki (he says things like *"ingerí 05-intro_robo_proba"*, *"agregá esta PDF al wiki"*, *"procesá Tutorial 8"*).
- **Writing or expanding a concept page** under `wiki/<module>/` (e.g. *"escribí la página de Filtro de Kalman"*, *"completá la sección de derivación en EKF"*, *"ampliá MCL con la parte del resampling"*).

The skill should run silently as part of writing the page; Valentino does not need to ask for screenshots explicitly.

Do NOT invoke this skill when:

- Valentino is only asking conceptually without writing to the wiki ("explicame X", "ayudame a entender Y").
- The work is on `index.md`, `index.md`, `log.md`, `_Overview.md`, `CLAUDE.md`, or `DESIGN.md` — those are meta files and do not get inline screenshots.
- He is editing existing pages without adding new content (typos, link fixes, etc.).
- He explicitly says "sin screenshots" / "no agregues imágenes".

## What this skill does

The flow has five steps. **Read all of them before acting** — the order matters, especially the verification steps before and after generation.

### Step 1 — Identify the source PDF

Sources live in `Raw/`, organized as:

- `Raw/Diapositivas/Teoricas/` — 14 lecture PDFs (`00-introduccion-3.pdf`, `01-algebra_lineal-2.pdf`, …, `12-mapas_de_ocupacion-2.pdf`).
- `Raw/Diapositivas/Tutoriales/` — 8 ROS2 tutorial PDFs (`Tutorial 1_ ...pdf` … `Tutorial 8_ ...pdf`).
- `Raw/Apuntes/` — classmate's parcial summary (single PDF, large, local-only).
- `Raw/TPs/` — three TP statements (`Enunciado TP1.pdf`, `Enunciado TP2.pdf`, `Enunciado TP3.pdf`).

The source PDF is determined by the page being written:

- If Valentino names a source explicitly (*"ingerí Teóricas/05-..."*), use that.
- If he names a concept (*"escribí la página de Kalman"*), pick the matching theoretical PDF (e.g. `Teoricas/10-filtro_de_kalman-3.pdf`) and any related tutorial (e.g. `Tutoriales/Tutorial 8_ KF, EKF, UKF.pdf`). Confirm with him in chat if ambiguous.

Use `Glob` on `Raw/**/*.pdf` to enumerate available files when needed.

### Step 2 — Decide whether any slide is worth capturing

The bar in this vault is **liberal but not infinite**: capture a slide when it adds something prose alone can't carry. Concretely, capture it if any of the following hold:

- It contains a **diagram, architecture, unrolled algorithm, side-by-side visual comparison, or sensor/robot photo**.
- It contains a **key formula or equation** as it appears in the slide (with the cátedra's notation, boxed, or with surrounding labels), and that formula anchors the section being written.
- It contains a **definition or boxed statement** that is the canonical phrasing of the concept.
- It contains a **worked example** (numerical instance, traced execution, comparison table) that the page is referencing.

Skip slides that are:

- Decorative covers, indices, agenda slides, or pure-text bullet lists with no visual structure.
- Slides whose content is fully captured in adjacent prose without losing meaning.

**Cap: 6–10 images per concept page**, more if every additional one clearly adds value. Saturating with marginal screenshots dilutes the high-value ones — restraint matters.

If no slide passes this bar for a given section, **write the section without any image**. Do not force an image just because the skill is active.

### Step 3 — Confirm the page number BEFORE generating

This step is critical. PDF page numbering can be off by one relative to the slide number shown in the slide footer (the PDF's first page is usually the cover, not slide 1). Generating the wrong slide and writing it to disk is **expensive to undo**: the bash sandbox does not have permission to delete files in the vault (`rm` returns `Operation not permitted`), so a wrong file becomes an orphan that Valentino has to delete manually from Obsidian or Finder.

**Before calling `pdftoppm`, always:**

1. Use the `Read` tool on the PDF with the `pages: "<N>"` parameter to view the candidate page directly.
2. Confirm the page contains the diagram/formula/etc. you intended to capture.
3. Only then proceed to Step 4.

If the page is wrong, scan ±2 pages to find the correct one. PDF page numbers from `pdftoppm`'s `-f`/`-l` are 1-indexed and typically match what `Read` returns with `pages:`.

### Step 4 — Extract the slide as PNG

Use `pdftoppm` from the bash sandbox. Render at 200 DPI for crisp text:

```bash
cd "wiki/<module>/Img" && \
pdftoppm -f <PAGE> -l <PAGE> -r 200 -png \
  "../../../Raw/Diapositivas/Teoricas/<PDF FILENAME>" "<descriptive name>"
```

Adjust the `..` count if the source PDF is in a different `Raw/` subfolder. Examples:
- Source in `Raw/Diapositivas/Teoricas/`: `../../../Raw/Diapositivas/Teoricas/<PDF>`
- Source in `Raw/Diapositivas/Tutoriales/`: `../../../Raw/Diapositivas/Tutoriales/<PDF>`
- Source in `Raw/Apuntes/`: `../../../Raw/Apuntes/<PDF>`
- Source in `Raw/TPs/`: `../../../Raw/TPs/<PDF>`

`pdftoppm` appends a page-number suffix to the filename (e.g., `KF predict update-9.png`). Rename the file to drop the suffix:

```bash
cd "wiki/<module>/Img" && \
mv "<descriptive name>-<PAGE>.png" "<descriptive name>.png"
```

After renaming, **read the resulting PNG with the `Read` tool** as a final safety net. Even after Step 3, render glitches happen — `pdftoppm` occasionally produces blank pages on PDFs with unusual layers. If the PNG is wrong, see "Recovering from a wrong capture" below.

**Path translation**: when working through Claude file tools the path is `/Users/varbelaiz/Obsidian/Robotica/...`; in the bash sandbox the same folder is `/sessions/<session>/mnt/Robotica/...`. Use the bash mount path for shell commands.

### Step 5 — Insert the image in the wiki page

Use Obsidian wikilink syntax with just the filename (Obsidian resolves images vault-wide), followed by an italic caption:

```markdown
![[KF predict-update.png]]
*Ciclo predict/update del Kalman, slide 9.*
```

Place the image directly under the section heading + grounding citation, before the prose explanation, when the image *introduces* the concept. Place it after the prose when the image *illustrates* a point already made.

## Naming convention

**Descriptive names without dates and without slide numbers.** Use a noun phrase that identifies the concept the slide illustrates.

Optional **concept prefix** when the same visual idea appears across modules (e.g. all the filters have a "predict/update" diagram): prefix with the concept short-name to disambiguate.

Examples that match the convention:

- `KF - predict update.png` — Kalman cycle.
- `EKF - jacobiano.png` — linearization step.
- `MCL - resampling.png` — particle resampling diagram.
- `Bayes - regla.png` — boxed statement of Bayes rule.
- `Sensor laser - haz.png` — diagram of a laser scanner's beam model.
- `Mapa de ocupación - grid.png` — example occupancy grid.

Do **not** use generic names like `slide-21.png`, `diagram.png`, `image1.png`, or include the lecture number from the PDF filename (e.g. avoid `10-KF.png`). The module folder context already disambiguates which module the image belongs to.

If a name collision exists (a file with the same name is already in the module's `Img/`), check whether the existing file is the same slide — if so, reuse it instead of regenerating. If it's a different slide that happens to share a concept, disambiguate with a more specific noun phrase.

## Recovering from a wrong capture

If you generate a PNG that turns out to be wrong (after reading it in Step 4), you cannot delete it — the bash sandbox returns `Operation not permitted` on `rm` against vault files. The recovery procedure is:

1. **Generate the correct image with a slightly different name** to avoid collision (e.g., add a one-word qualifier: `KF - predict update detallado.png` instead of `KF - predict update.png`).
2. **Reference the new name** in the wiki page. The wrong file becomes an orphan but does not affect the page's rendering.
3. **Tell Valentino at the end** of the response that there is an orphaned file in `wiki/<module>/Img/` (give the exact filename) so he can delete it manually from Obsidian or Finder. The lint workflow in `CLAUDE.md` will also surface orphans on its next run.

Do not try `mv` to overwrite the bad file — it works, but losing the original name is worse than telling Valentino once. The clean Step 3 verification *before* generation is the real fix; this recovery procedure is for the rare case it slips through.

## Concrete example

Input request: *"escribí la página de Filtro de Kalman ingiriendo `Teoricas/10-filtro_de_kalman-3.pdf`"*

The page will need visuals for: the predict/update cycle, the Kalman gain derivation, and a comparison of prediction-only vs corrected-by-observation Gaussians.

Steps:

1. Identify PDF: `Raw/Diapositivas/Teoricas/10-filtro_de_kalman-3.pdf`.
2. Use `Read` with `pages: "9"` on the PDF to confirm slide 9 has the predict/update diagram (and not e.g. an algorithm pseudocode).
3. Generate the image:
   ```bash
   cd "/sessions/<session>/mnt/Robotica/wiki/5. Filtros Bayesianos/Img" && \
   pdftoppm -f 9 -l 9 -r 200 -png \
     "../../../Raw/Diapositivas/Teoricas/10-filtro_de_kalman-3.pdf" "KF - predict update" && \
   mv "KF - predict update-9.png" "KF - predict update.png"
   ```
4. Verify with `Read` on the resulting PNG.
5. Edit the wiki page to insert under the relevant section:
   ```markdown
   ![[KF - predict update.png]]
   *Ciclo predict/update del Kalman, slide 9.*
   ```
6. Repeat for the other 2–8 visuals planned for the page.

## Why this skill exists

Valentino's wiki is a study tool grounded strictly in the source PDFs (see the grounding rule in `CLAUDE.md`). Visual reinforcement matters disproportionately in robotics, where diagrams (sensor beam models, occupancy grids, particle distributions, Kalman gain geometry) carry intuitions that prose can only approximate. Doing this by hand every time is mechanical and slows down the actual study workflow.

The constraints (descriptive names, ~6–10 images per page, only slides that pass the bar, only when writing to wiki concept pages) exist because oversaturating the vault with images dilutes their value. Restraint is the point — every screenshot should earn its place.

The verification steps (Step 3 *before* generation, the `Read` after Step 4) exist because the vault is read/write but not delete from the sandbox: a wrong capture becomes a permanent orphan that Valentino has to clean up manually. Catching mistakes before they hit disk is the only clean path.
````

- [ ] **Step 3: Verify the file looks well-formed**

Use `Read` on `.claude/skills/slide-screenshot/SKILL.md` and confirm: frontmatter valid, no leftover NLP-specific terms (`Preguntas.md`, `Imagenes/`, `CLASE N`), Robótica paths and folder names present.

### Task 9: Commit Phase 3

- [ ] **Step 1: Stage and verify**

```bash
git add .claude/skills/slide-screenshot/SKILL.md
git status --short
```

Expected:
```
M  .claude/skills/slide-screenshot/SKILL.md
```

- [ ] **Step 2: Commit**

```bash
git commit -m "$(cat <<'EOF'
Adapt slide-screenshot skill from NLP patterns to Robotica vault

Changes:
- Trigger: now fires when ingesting a Raw/ source or writing/expanding
  a concept page in wiki/<module>/, instead of writing answers to
  Preguntas.md (which does not exist in this vault).
- Source PDFs: live in Raw/Diapositivas/{Teoricas,Tutoriales}/,
  Raw/Apuntes/, Raw/TPs/ instead of one PDF per class folder.
- Output folder: wiki/<module>/Img/ instead of <class folder>/Imagenes/.
- Cap relaxed: 6-10 images per page (more if every one adds value),
  vs 3 in the NLP version.
- Bar broadened: also captures key formulas, definitions, worked
  examples, sensor/robot photos — not only diagrams.
- Path translation: /Users/varbelaiz/Obsidian/Robotica/...
  <-> /sessions/<session>/mnt/Robotica/...
- Naming: optional concept prefix to disambiguate across modules.
- Mechanical guarantees preserved: pre-render Read of the PDF page,
  post-render Read of the PNG, orphan recovery procedure, descriptive
  naming without dates/slide numbers.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
EOF
)"
```

---

## Phase 4 — Push to remote

### Task 10: Push the three new commits to `origin/main`

- [ ] **Step 1: Push**

```bash
git push origin main
```

Expected:
```
To https://github.com/varbelaiz/robotics-vault.git
   <hash>..<hash>  main -> main
```

- [ ] **Step 2: Verify on GitHub**

```bash
gh repo view varbelaiz/robotics-vault --web
```

(Optional — opens the repo in the browser. Confirm the three commits are there.)

---

## Definition of Done

- `index.md`, `index.md`, `log.md` exist at vault root and render in Obsidian.
- `Welcome.md` is removed from disk and from git.
- All 7 module folders exist under `wiki/`, each with a skeleton `_Overview.md` and an empty `Img/` folder tracked via `.gitkeep`.
- The `slide-screenshot` skill at `.claude/skills/slide-screenshot/SKILL.md` references Robótica paths, conventions, and trigger conditions — no leftover NLP-specific terms.
- Three new commits on `main`, all pushed to `origin/main`.

After Done: the next user action is to trigger the **first ingestion** of `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf` following the Ingestion workflow defined in `CLAUDE.md` (read PDF → discuss takeaways → propose plan → execute on approval → log). The introductory lecture `00-introduccion-3.pdf` is intentionally skipped — Valentino indicated it does not enter the parcial.

---

## Self-Review Checklist (filled in after writing the plan)

**Spec coverage:** Walked through DESIGN.md and CLAUDE.md and confirmed every bootstrap item is covered: meta files (Tasks 1–3), Welcome cleanup (Task 4), 7 module skeletons with `_Overview.md` + `Img/` (Task 6), skill adaptation (Task 8), git commits + push (Tasks 5, 7, 9, 10). Page-template specifics, grounding rule, screenshot policy, workflows — all live in `CLAUDE.md` already and are not duplicated here.

**Placeholder scan:** No "TBD", "TODO", "implement later", "Add appropriate error handling" patterns. Every file gets its full content shown inline. The `_Overview.md` skeletons intentionally contain *(narrativa pendiente)* text — these are content placeholders for future ingestions, not plan placeholders, and are explicitly justified by the grounding rule.

**Type consistency:** Checked that module folder names, `_Overview` filename, `Img/` folder name, wikilink syntax, and frontmatter field names match between this plan and `CLAUDE.md`. No drift.
