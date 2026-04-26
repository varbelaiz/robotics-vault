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
