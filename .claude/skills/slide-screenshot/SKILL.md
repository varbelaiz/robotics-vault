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
- The work is on `Home.md`, `index.md`, `log.md`, `_Overview.md`, `CLAUDE.md`, or `DESIGN.md` — those are meta files and do not get inline screenshots.
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
