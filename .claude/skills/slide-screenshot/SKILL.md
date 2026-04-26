---
name: slide-screenshot
description: Captures relevant slide diagrams from class PDFs and embeds them inline when writing answers in the NLP Obsidian vault. Use this skill whenever Valentino asks to write, fill in, or save an answer in a `Preguntas.md` file (phrases like "escribí la respuesta en el archivo", "respondé y guardalo", "completá esta pregunta en el archivo"). The skill handles the full mechanical loop — identifying which slide pages contain visual content worth showing (diagrams, architectures, unrolled networks, side-by-side comparisons), extracting them as PNG with `pdftoppm`, naming them per the vault convention, and inserting them with Obsidian wikilink syntax. Use even when Valentino doesn't explicitly ask for screenshots — that's the point of the skill, to do this automatically when relevant.
---

# Slide Screenshot Skill

Captures slide images from the class PDF of the relevant NLP class folder and embeds them in the answer being written to `Preguntas.md`.

## When to invoke this skill

Trigger this skill when Valentino asks to write or save an answer in `Preguntas.md` — phrases like *"escribí la respuesta en el archivo"*, *"respondé esta pregunta y guardala"*, *"completá esta en el archivo"*. The skill should run silently as part of writing the answer; Valentino does not need to ask for screenshots explicitly.

Do NOT invoke this skill when:

- Valentino is only asking the question conversationally without asking to save it ("explicame X", "ayudame a entender Y").
- The user is editing existing answers without adding new content.
- The work is on `Resumen.md`, `<Tema> - Desarrollo.md`, or `Home.md` — those have their own conventions and don't follow this image flow.

## What this skill does

The flow has five steps. **Read all of them before acting** — the order matters, especially the verification steps before and after generation.

### Step 1 — Identify the class folder and PDF

The vault is organized as `N. <Topic>/` folders, each containing one PDF named like `CLASE N -<...>.pdf`. The current class is determined by which `Preguntas.md` is being edited. The PDF in that same folder is the source of truth for slide content.

Use `Glob` on `<class folder>/CLASE *.pdf` to find the exact filename — names vary slightly between classes.

### Step 2 — Decide whether any slide is worth capturing

Most answers do **not** need an image. Citing the slide by page number (*"slides pág. 22"*) is enough for textual content. Only capture a slide when **all** of these are true:

- The slide contains a **diagram, architecture, unrolled network, side-by-side visual comparison, or worked-out equation that would lose information if reduced to a text description**.
- The diagram directly illustrates the concept the answer is explaining.
- A reader of the answer would understand the concept faster *with* the image than without it.

Skip slides that are mostly bullet points, titles, or text — those are better cited inline.

**Maximum 3 images per answer.** If more than 3 candidate slides exist, pick the ones that show the most distinct visual ideas. Saturating the note with images defeats the purpose.

If no slide passes this bar, **write the answer without any image**. Do not force an image just because the skill is active.

### Step 3 — Confirm the page number BEFORE generating

This step is critical. PDF page numbering can be off by one relative to the slide number shown in the slide footer (the PDF's first page is usually the cover, not slide 1). Generating the wrong slide and writing it to disk is **expensive to undo**: the bash sandbox does not have permission to delete files in the vault's `Imagenes/` folder (`rm` returns `Operation not permitted`), so a wrong file becomes an orphan that Valentino has to delete manually from Obsidian/Finder.

**Before calling `pdftoppm`, always:**

1. Use the `Read` tool on the PDF with the `pages: "<N>"` parameter to view the candidate page directly.
2. Confirm the page contains the diagram you intended to capture.
3. Only then proceed to Step 4.

If the page is wrong, scan ±2 pages to find the correct one. PDF page numbers from `pdftoppm`'s `-f`/`-l` are 1-indexed and typically match what `Read` returns with `pages:`.

### Step 4 — Extract the slide as PNG

Use `pdftoppm` from the bash sandbox. Render at 200 DPI for crisp text:

```bash
cd "<class folder>/Imagenes" && \
pdftoppm -f <PAGE> -l <PAGE> -r 200 -png \
  "../<PDF FILENAME>" "<descriptive name>"
```

`pdftoppm` appends a page-number suffix to the filename (e.g., `Generacion de texto-21.png`). Rename the file to drop the suffix:

```bash
cd "<class folder>/Imagenes" && \
mv "<descriptive name>-<PAGE>.png" "<descriptive name>.png"
```

After renaming, **read the resulting PNG with the `Read` tool** as a final safety net. Even after Step 3, render glitches happen — `pdftoppm` occasionally produces blank pages on PDFs with unusual layers. If the PNG is wrong, see "Recovering from a wrong capture" below.

Path translation: when working through Obsidian/file tools the path is `/Users/varbelaiz/Obsidian/NLP/...`; in the bash sandbox the same folder is `/sessions/<session>/mnt/NLP/...`. Use the bash mount path for shell commands.

### Step 5 — Insert the image in `Preguntas.md`

Use Obsidian wikilink syntax with just the filename (Obsidian resolves images vault-wide):

```markdown
![[Nombre descriptivo.png]]
```

Place the image directly under the paragraph it supports — usually right after the prose introduction of the diagram, before any list or equation that elaborates on it.

## Naming convention

**Descriptive names without dates.** Use a noun phrase that identifies the concept the slide illustrates. Match the casing and phrasing style of existing files in `Imagenes/`.

Examples that match the convention:

- `Generacion de texto.png` — the unrolled RNN generating *"Yo quiero ver un tren"*.
- `Celda LSTM.png` — the LSTM cell with gates labeled.
- `Seq2Seq traduccion.png` — encoder-decoder pipeline for translation.
- `RNN bidireccional.png` — the two-direction unrolled diagram.

Do **not** use generic names like `slide-21.png`, `diagram.png`, `image1.png`, or include the class number. The folder context already disambiguates which class the image belongs to.

If a name collision exists (a file with the same name is already in `Imagenes/`), check whether the existing file is the same slide — if so, reuse it instead of regenerating. If it's a different slide that happens to share a concept, disambiguate with a more specific noun phrase (e.g., `Celda LSTM gates.png` vs. `Celda LSTM ecuaciones.png`).

## Recovering from a wrong capture

If you generate a PNG that turns out to be wrong (after reading it in Step 4), you cannot delete it — the bash sandbox returns `Operation not permitted` on `rm` against vault files. The recovery procedure is:

1. **Generate the correct image with a slightly different name** to avoid collision (e.g., add a one-word qualifier: `Preentrenados ELMo menos datos.png` instead of `Preentrenados menos datos.png`).
2. **Reference the new name** in `Preguntas.md`. The wrong file becomes an orphan but does not affect the answer's rendering.
3. **Tell Valentino at the end** that there is an orphaned file in `Imagenes/` (give the exact filename) so he can delete it manually from Obsidian or Finder.

Do not try `mv` to overwrite the bad file — it works, but losing the original name is worse than telling Valentino once. The clean Step 3 verification *before* generation is the real fix; this recovery procedure is for the rare case it slips through.

## Concrete example

Input request: *"Respondé esta pregunta y guardala en el archivo: ¿Cómo se usa una RNN para generación de texto?"*

The answer involves the autoregressive generation loop. The unrolled RNN diagram with `<start>`, `Yo quiero ver un tren .`, and `<end>` is a strong candidate — the diagram conveys the autoregressive flow more directly than text.

Steps:

1. Find the PDF: `3. RNNs/CLASE 3 -Language models 2 (RNN).pptx.pdf`.
2. Use `Read` with `pages: "21"` on the PDF to confirm slide 21 is the full unrolled diagram (not the partial intermediate version on slides 17-20).
3. Generate the image:
   ```bash
   cd "/sessions/<session>/mnt/NLP/3. RNNs/Imagenes" && \
   pdftoppm -f 21 -l 21 -r 200 -png \
     "../CLASE 3 -Language models 2 (RNN).pptx.pdf" "Generacion de texto" && \
   mv "Generacion de texto-21.png" "Generacion de texto.png"
   ```
4. Verify with `Read` on the resulting PNG.
5. Edit `Preguntas.md` to insert `![[Generacion de texto.png]]` right after the introductory paragraph that mentions the autoregressive procedure.

## Why this skill exists

Valentino's notes are a study tool he revisits for exams. Visual reinforcement matters for architectures (RNN unrolled, LSTM gates, encoder-decoder, BiLSTM). Doing this by hand every time is mechanical and slows down the actual study workflow. The skill keeps the visual quality of the notes high without making Valentino break flow to fiddle with `pdftoppm` and Obsidian image syntax.

The constraints (descriptive names, max 3 images, only diagrams, only when writing to `Preguntas.md`) exist because oversaturating the vault with images dilutes their value — when *every* answer has 5 screenshots, none of them stand out. Restraint is the point.

The verification steps (Step 3 *before* generation, the `Read` after Step 4) exist because the vault is read/write but not delete from the sandbox: a wrong capture becomes a permanent orphan that Valentino has to clean up manually. Catching mistakes before they hit disk is the only clean path.
