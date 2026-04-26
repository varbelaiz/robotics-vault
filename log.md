# Bitácora del Wiki

> Append-only. Una entrada por operación.
> Formato: `## [YYYY-MM-DD] {ingest|consulta|lint|bootstrap} | <título corto>`
> Recientes: `grep "^## \[" log.md | tail -10`

> [[Home|← Inicio]]

---

## [2026-04-26] bootstrap | inicialización del wiki
- Creados meta files: `Home.md`, `index.md`, `log.md`
- Creados esqueletos de los 7 módulos (`_Overview.md` + `Img/`)
- Skill `slide-screenshot` adaptada de NLP a Robótica
- Eliminado `Welcome.md` placeholder default de Obsidian
- Repo: `varbelaiz/robotics-vault` (privado), trackea `origin/main`
- Próximo paso: primera ingesta de `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf` (la 00-introduccion se omite — no entra en el parcial)

## [2026-04-26] ingest | Teóricas 01 - Algebra Lineal
- Fuente: `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf` (41 slides)
- Páginas conceptuales creadas (6) en `wiki/1. Fundamentos/`:
  - `Vectores.md` (slides 2–6)
  - `Matrices.md` (slides 7–20)
  - `Matriz Definida Positiva.md` (slides 21–22)
  - `Matriz Jacobiana.md` (slides 23–24)
  - `Rotaciones.md` (slides 25–31)
  - `Transformaciones Homogéneas.md` (slides 32–40)
- Actualizado: `_Overview.md` (M1) con tagline, narrativa, recorrido sugerido y lista de páginas.
- Actualizado: `Home.md` (M1 estado: Esqueleto → En progreso).
- Actualizado: `index.md` (entradas de M1).
- Screenshots capturadas: 29 PNGs en `wiki/1. Fundamentos/Img/`.
- Forward-references a páginas aún no creadas (resolverán en futuras ingestas): `[[EKF]]`, `[[Filtro de Kalman]]`, `[[MCL - Filtro de Partículas]]`.

## [2026-04-26] doc | tightening del pipeline de ingesta
- Expandida la sección `Workflows → Ingestion` de `CLAUDE.md` con detalle por fase (page-count discovery, cap de 20 págs del Read tool, orden de ejecución, gotchas, referencia al ingest M1 como worked example, compatibilidad con plan mode).
- `slide-screenshot/SKILL.md` Step 4 reescrito: usa `pdftoppm -singlefile` + absolute paths (elimina el `mv` y el `cd`). Removido el bloque de "path translation" obsoleto (era heredado del vault de NLP, no aplica a este entorno).
- Concrete example en SKILL.md actualizado a la nueva sintaxis.
- Objetivo: que la doc sea autosuficiente para correr ingestas en plan mode en sesiones futuras sin contexto previo.

## [2026-04-26] ingest | Teóricas 03,04,06,07 - M2 Locomoción + M3 Sensores y Modelos
- Fuentes:
  - `Raw/Diapositivas/Teoricas/03-locomocion-3.pdf` (35 slides)
  - `Raw/Diapositivas/Teoricas/04-sensores-3.pdf` (28 slides)
  - `Raw/Diapositivas/Teoricas/06-modelos-de-movimiento_con_modelo_velocidad-3.pdf` (48 slides)
  - `Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf` (32 slides)
- Páginas conceptuales creadas (5) en `wiki/2. Locomoción/`:
  - `Locomoción y Tipos de Accionamientos.md` (PDF 03 slides 2–8)
  - `Cinemática del Robot Diferencial.md` (PDF 03 slides 9–23)
  - `Otros Accionamientos - Ackermann, Síncrono, Omni.md` (PDF 03 slides 24–33)
  - `Odometría y Modelo de Movimiento (Odometría).md` (PDF 03 slides 34–35 + PDF 06 slides 1–32)
  - `Modelo de Movimiento (Velocidad).md` (PDF 06 slides 33–48)
- Páginas conceptuales creadas (5) en `wiki/3. Sensores y Modelos/`:
  - `Sensores Internos.md` (PDF 04 slides 2–9, 28)
  - `Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras.md` (PDF 04 slides 10–27)
  - `Modelo de Sensor Basado en Haz.md` (PDF 07 slides 1–20)
  - `Modelo de Campo de Verosimilitud.md` (PDF 07 slides 21–26)
  - `Modelo de Detección de Landmarks.md` (PDF 07 slides 27–31)
- `_Overview.md` reescritos para M2 y M3 (tagline, narrativa, recorrido sugerido, conexiones, lista de páginas).
- `Home.md`: M2 y M3 estado `Esqueleto → Completo`.
- `index.md`: entradas para las 10 páginas nuevas, agrupadas por módulo.
- Screenshots capturadas: 46 PNGs en `wiki/2. Locomoción/Img/`, 37 en `wiki/3. Sensores y Modelos/Img/` (83 totales).
- Forward-references a páginas aún no creadas (resolverán en futuras ingestas):
  `[[Filtro de Kalman]]`, `[[EKF]]`, `[[MCL - Filtro de Partículas]]`, `[[Filtros Discretos]]`,
  `[[Regla de Bayes]]`, `[[Mapa de Ocupación]]`, `[[Tutorial 6 - Movimiento, Sensores e Histogramas]]`.
- Decisiones de partición:
  - PDF 06 (modelos de movimiento) entró a M2, no a M5 — alineado con la guía del `_Overview` original ("los modelos de movimiento alimentan los filtros").
  - PDF 02 (paradigmas) queda fuera de este batch — no encaja limpio en M2 ni M3.

## [2026-04-26] doc | nueva skill project-local "ingest"
- Creada `.claude/skills/ingest/SKILL.md`: encapsula el runbook completo de ingesta como skill auto-triggered (phrases tipo "ingerí X", "agregá esta PDF al wiki", "procesá Tutorial Y", "hacé el ingest de TP2").
- `CLAUDE.md` → `Workflows → Ingestion` slimmeado a un puntero de ~10 líneas hacia la skill (antes ~80 líneas in-place).
- División de concerns clara: `CLAUDE.md` mantiene templates A/B/C/D + reglas duras + frontmatter spec. La skill `ingest` es la operación. La skill `slide-screenshot` es la mecánica de imágenes.
- Plan-mode-friendly por construcción: fases 1–3 read-only para el plan window, phase 4 es el ExitPlanMode gate, fases 5–6 ejecutan tras aprobación.
