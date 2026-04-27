# Bitácora del Wiki

> Append-only. Una entrada por operación.
> Formato: `## [YYYY-MM-DD] {ingest|consulta|lint|bootstrap} | <título corto>`
> Recientes: `grep "^## \[" log.md | tail -10`

> [[Robotica|← Inicio]]

---

## [2026-04-27] audit+ingest | M3, M5, M6 — fixes de cobertura y broken images
- **Disparador**: Valentino pidió auditar los 3 módulos restantes después del audit M2/M4.
- **Método**: 3 agentes Explore en paralelo, uno por módulo. Verifiqué cada claim antes de actuar — uno alucinó que `[[EKF]]` y `[[MCL]]` no existían (sí existen en M5).
- **M6 — fix urgente**:
  - **3 imágenes broken** en `Mapeo con Poses Conocidas.md` que rompían el render: `mapas-slide-05.png`, `mapas-slide-42.png`, `mapas-slide-43.png` referenciadas pero inexistentes en `Img/`. Capturadas del PDF 12 (slides 5, 42, 43).
  - **Mapas de Ocupación.md sec 3 ("Notación Log Odds")**: expandida a "Derivación a la regla recursiva" con: (a) la derivación con cocientes (slides 26–28: Bayes → Markov → Bayes → Markov → cociente con cancelación de términos), (b) fórmula de recuperación $p(x) = 1 - 1/(1+\exp l(x))$ (slide 30), (c) regla recursiva en log odds como suma (slides 31–32).
  - **SLAM y Exploración**: limpieza de fuentes — `00-introduccion-3.pdf` no se consume directamente; Exploración pasa a `fuentes: []` con callout "Página puente" (landing legítima sin grounding directo).
- **M3 — gap puntual**:
  - **Modelo de Sensor Basado en Haz.md**: agregada sec 8 "Influencia del ángulo al obstáculo" (slides 16–19 PDF 07: 4 plots 3D que muestran cómo varía $p(z|x,m)$ según el ángulo de incidencia). Capturado slide 16 como representativo (`Beam - influencia angulo.png`); los 17–19 son la misma estructura con sonar-1/2/3, una imagen alcanza. Sec "Resumen y limitaciones" renumerada a 9.
  - **Descarté**: el agente reportó "frontmatter ausente en `Sensores y Modelos.md`" como bug — verifiqué que **ningún overview de módulo** tiene frontmatter (Locomoción, Robótica Probabilística, Mapeo tampoco). Es por diseño.
- **M5 — UKF corrección formal**:
  - **UKF.md sec 6**: el algoritmo de predicción y corrección estaba descrito en lenguaje natural sin las fórmulas. Reescrito con las ecuaciones explícitas del PDF 11b: (a) predicción con sigma points + reconstrucción + $R_t$ (slide 28), (b) corrección con regeneración de sigma points + propagación por $h$ + observación esperada $\hat{z}_t$ + covarianza de innovación $S_t$ + covarianza cruzada $\bar{\Sigma}_t^{x,z}$ + ganancia $K_t$ + actualización (slides 30–31). Agregado callout que muestra la equivalencia conceptual con el EKF ($\bar{\Sigma}_t^{x,z}$ ↔ $\bar{\Sigma}_t H_t^T$, slide 32).
- **Cobertura final**:
  - M3 → ~98% (PDF 04 100%, PDF 07 ahora 95% con slide 16 cubierto; quedan los slides 17–19 redundantes con 16)
  - M5 → ~92% (UKF corrección ahora completo; gaps menores de UKF restantes son cosmética/visualizaciones que las imágenes ya capturan)
  - M6 → ~92% (broken links resueltos; derivación log odds completa; SLAM/Exploración correctamente declaradas como landing pages)
- **Decisiones**:
  - 3 audits + fixes en un solo commit (modules son ortogonales pero los cambios son chiquitos cada uno; un commit por módulo era overkill).
  - No re-stagiar los cambios anteriores de `Robotica.md` — el catálogo no cambió de páginas, sólo se editó contenido in-place.

## [2026-04-27] audit+lint | M4 — integración de Derivación del Filtro de Bayes + lint cosmético
- **Disparador**: pedido de Valentino de auditar el resto de archivos en `Robótica Probabilística`.
- **Auditoría de cobertura**:
  - PDF 05 parte 1 (31 slides) → 100% cubierto (verificado en audit anterior).
  - PDF 05 parte 2 (15 págs, slides 31–45 con cover) → 100% cubierto entre `Filtro de Bayes.md` (slides 31, 41–43, 45), `Suposición de Markov.md` (slide 32) y `Derivación del Filtro de Bayes.md` (slides 34–40).
- **Hallazgo principal**: `Derivación del Filtro de Bayes.md` existía completa pero **untracked** y **huérfana** — no estaba integrada en el overview ni cross-linkeada desde `Filtro de Bayes.md`. Su contenido ya estaba listado en el catálogo de `Robotica.md` pero no en `Robótica Probabilística.md` (overview del módulo) ni en `Páginas en este módulo`.
- **Cambios**:
  - `Robótica Probabilística.md`: agregado paso 10 al *Recorrido sugerido* y entrada en *Páginas en este módulo*. M4 ahora 12/12.
  - `Filtro de Bayes.md`: cross-link a `Derivación del Filtro de Bayes` en sec *Conexiones* + nota inline al final de la subsección de derivación. Fix typo "Si **Se desea**" → "**Se desea**".
  - `Introducción a la Robótica Probabilística.md`: limpieza de `fuentes:` (sólo cita pág. 2 de parte 1; se quitó parte 2 que aparecía sin sección concreta — la página no la consume directamente).
  - `Suposición de Markov.md`: refinada la cita a parte 1 pág. 21 (es la primera aparición como tag, no la diapositiva propia) y a parte 2 pág. 32 (definición formal con DBN).
- **Decisiones**:
  - El catálogo de `Robotica.md` listaba 12 páginas en M4 pero la tabla de estado mostraba 11/11 — *no* lo corrijo ahora porque la página untracked aún debe commiteársela; se ajusta en este mismo commit a 12/12.
  - No toqué `Modelo de Sensor.md` (es deliberadamente landing page hacia M3) ni capturé los slides 11/12 (Ley prob total / Marginalización) — las fórmulas LaTeX en `Probabilidad Conjunta.md` ya las muestran sin pérdida.

## [2026-04-27] audit+ingest | M2 — gap de PDF 06 (slides 5, 6, 14, 17–22, 25–27, 47)
- **Disparador**: Valentino observó que `wiki/4. Robótica Probabilística/Modelo de Movimiento.md` no citaba `06-modelos-de-movimiento_con_modelo_velocidad-3.pdf` y pidió auditar gaps.
- **Auditoría**:
  - PDF 05 (`05-intro_robo_proba-parte_1.pdf`, 31 slides) → 100% cubierta entre las 11 páginas de M4. La cita exclusiva de PDF 05 en `Modelo de Movimiento.md` (M4) es **correcta** porque esa página es el modelo abstracto (FSM puerta, slides 23–31). Las instancias concretas viven en M2.
  - PDF 06 (48 slides) → ~85% cubierta. Gaps reales: slides 5–6 (sistemas de coordenadas 3D/planar), 14 (algoritmos formales `prob_normal_distribution`/`prob_triangular_distribution`), 17–22 (representación por muestras + `sample_normal_distribution`/`sample_triangular_distribution`), 25–27 (algoritmo formal `sample_distribution` + ejemplos `abs(x)`, `cos(x)`), 47 (modelo consistente con mapas para velocidad).
- **Páginas creadas**:
  - `wiki/2. Locomoción/Muestreo de Distribuciones.md` — hub de las operaciones `prob` y `sample` (normal/triangular/rejection). Cubre slides 13, 14, 17–27 de PDF 06. Pensada como referencia única para `Odometría`, `Velocidad`, `MCL`, `TP2`.
- **Páginas editadas**:
  - `Odometría y Modelo de Movimiento (Odometría).md`: secciones 6 (distribuciones) y 9 (rejection sampling) reemplazadas por punteros al hub. Frontmatter actualizado a 2026-04-27. Cita de slides 13, 23–25 movida al hub.
  - `Modelo de Movimiento (Velocidad).md`: agregada sec 9 "Modelo consistente con mapas" (slide 47, reutiliza imagen de `Odometría` sec 12). Sección "Variantes y conexiones" renumerada a sec 10. Frontmatter 2026-04-27.
  - `Cinemática del Robot Diferencial.md`: sec 2 ampliada con preámbulo "Del 3D al planar" (slide 5: Euler vs RPY, link a [[Cuaterniones]]/[[Rotaciones]]) + "Restricción a la superficie plana" (slide 6). Frontmatter incluye PDF 06 como segunda fuente.
  - `Locomoción.md`: agregado paso 6 al recorrido y entrada en lista de páginas.
  - `Robotica.md`: M2 → 6/6 páginas, catálogo extendido con `Muestreo de Distribuciones`.
- **Capturas nuevas** (11 PNGs en `wiki/2. Locomoción/Img/`):
  - `Coordenadas - 3D Euler RPY.png` (slide 5), `Coordenadas - planar xytheta.png` (slide 6)
  - `Muestreo - prob algoritmos.png` (slide 14), `Muestreo - densidad muestras.png` (slide 18)
  - `Muestreo - sample normal algoritmo.png` (slide 19), `Muestreo - histograma normal.png` (slide 20)
  - `Muestreo - sample triangular algoritmo.png` (slide 21), `Muestreo - histograma triangular.png` (slide 22)
  - `Muestreo - sample distribution algoritmo.png` (slide 25), `Muestreo - ejemplo abs.png` (slide 26), `Muestreo - ejemplo cos.png` (slide 27)
- **Decisiones**:
  - Hub centralizado en M2 (no M5) porque sus consumidores principales son `Odometría` y `Velocidad` — ambos viven en M2. `MCL` lo referencia hacia atrás vía wikilink.
  - Preámbulo de coordenadas se sumó a `Cinemática del Robot Diferencial.md` (no a `Locomoción.md` ni a una página nueva): ya tiene la sección "Pose 2D" que era el lugar natural.
  - `Modelo de Movimiento.md` (M4) **no** se tocó. Su scope abstracto es correcto y ya tiene cross-link a las instancias en su sección "Conexiones".
- **No se hizo** (saltable): slides 32 y 48 (resúmenes) — no aportan contenido docente nuevo.

## [2026-04-26] bootstrap | inicialización del wiki
- Creados meta files: `Robotica.md`, `Robotica.md`, `log.md`
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
- Actualizado: `Robotica.md` (M1 estado: Esqueleto → En progreso).
- Actualizado: `Robotica.md` (entradas de M1).
- Screenshots capturadas: 29 PNGs en `wiki/1. Fundamentos/Img/`.
- Forward-references a páginas aún no creadas (resolverán en futuras ingestas): `[[EKF]]`, `[[Filtro de Kalman]]`, `[[MCL - Filtro de Partículas]]`.

## [2026-04-26] doc | tightening del pipeline de ingesta
- Expandida la sección `Workflows → Ingestion` de `CLAUDE.md` con detalle por fase (page-count discovery, cap de 20 págs del Read tool, orden de ejecución, gotchas, referencia al ingest M1 como worked example, compatibilidad con plan mode).
- `slide-screenshot/SKILL.md` Step 4 reescrito: usa `pdftoppm -singlefile` + absolute paths (elimina el `mv` y el `cd`). Removido el bloque de "path translation" obsoleto (era heredado del vault de NLP, no aplica a este entorno).
- Concrete example en SKILL.md actualizado a la nueva sintaxis.
- Objetivo: que la doc sea autosuficiente para correr ingestas en plan mode en sesiones futuras sin contexto previo.

## [2026-04-26] ingest | Teóricas 05 partes 1+2 - M4 Robótica Probabilística
- Fuentes:
  - `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` (31 slides)
  - `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf` (15 págs PDF, slides 31–45 con cover)
- Páginas conceptuales creadas (5) en `wiki/4. Robótica Probabilística/`:
  - `Probabilidad - Axiomas y Variables Aleatorias.md` (parte 1 slides 2–12)
  - `Regla de Bayes.md` (parte 1 slides 13–19)
  - `Actualización Recursiva Bayesiana.md` (parte 1 slides 20–22)
  - `Modelado de Acciones.md` (parte 1 slides 23–31)
  - `Filtro de Bayes.md` (parte 1 slide 31 + parte 2 slides 32–45)
- Actualizado: `_Overview.md` (M4) reescrito con tagline, narrativa, recorrido, conexiones, lista de páginas.
- Actualizado: `Home.md` (M4 estado: Esqueleto → Completo).
- Actualizado: `index.md` (entradas de M4).
- Screenshots capturadas: 20 PNGs en `wiki/4. Robótica Probabilística/Img/`.
- Resuelve forward-references previas: `[[Regla de Bayes]]` (referenciado desde *Odometría y Modelo de Movimiento (Odometría)*, *Modelo de Movimiento (Velocidad)*, *Modelo de Sensor Basado en Haz*).
- Deja preparado `[[Filtro de Bayes]]` como hub para M5; nuevas forward-refs a `[[Filtro de Kalman]]`, `[[MCL - Filtro de Partículas]]`, `[[Filtros Discretos]]` (M5).
- Recovery: las 4 PNGs de parte 2 inicialmente extraídas con offset incorrecto (parte 2 tiene su propia cover en p1, slides empiezan en p2). Re-extraídas con `pdftoppm -singlefile` (overwrite).

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
- `Robotica.md`: M2 y M3 estado `Esqueleto → Completo`.
- `Robotica.md`: entradas para las 10 páginas nuevas, agrupadas por módulo.
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

## [2026-04-27] refactor | split M7 → M7 (ROS2) + M8 (TPs)
- **Motivación**: el nombre "ROS2 y TPs" mezclaba dos cosas conceptualmente distintas — el framework + tutoriales (mecánica de ROS2) vs. los entregables del curso (TPs). Trade-off considerado: separación clara vs. coherencia de la secuencia de tutoriales.
- **Decisión**: opción **B** — separar TPs a M8, dejar tutoriales junto al framework en M7 (la secuencia T1→T8 se mantiene intacta).
- **Movimientos físicos**:
  - Carpeta: `wiki/7. ROS2 y TPs/` → `wiki/7. ROS2/`. Overview: `ROS2 y TPs.md` → `ROS2.md`.
  - Nueva carpeta: `wiki/8. TPs/Img/`.
  - 3 TPs movidos: `TP1`, `TP2`, `TP3` → `wiki/8. TPs/`.
  - 4 imágenes movidas: las 4 PNGs de TPs (`TP2 - rviz...`, `TP3 - rviz...`) → `wiki/8. TPs/Img/`.
- **Wikilinks updates** (4 pasadas con `sed`):
  - 18 archivos de M7: `[[ROS2 y TPs|...]]` → `[[ROS2|...]]`, frontmatter `modulo: 7. ROS2 y TPs` → `modulo: 7. ROS2`, alias `← ROS2 y TPs` → `← ROS2`, título `# Módulo 7 — ROS2 y TPs` → `# Módulo 7 — ROS2`.
  - 3 TPs en M8: `modulo: 8. TPs`, back-link `[[TPs|← TPs]]`.
  - Cross-module refs en M2 + M5: `[[7. ROS2 y TPs/ROS2 y TPs|Módulo 7: ROS2 y TPs]]` → `[[7. ROS2/ROS2|Módulo 7: ROS2]]`.
- **Páginas nuevas/reescritas**:
  - `wiki/8. TPs/TPs.md` (overview Template B) — recorrido sugerido + conexiones a todos los M1–M7.
  - `wiki/7. ROS2/ROS2.md` (overview reescrito) — sin sección de TPs, con énfasis en que los tutoriales aterrizan los modelos teóricos.
- **Catálogo `Robotica.md`**: tabla de status splitteada en M7 (18 págs) + M8 (4 págs). Sección 7 sin TPs. Nueva sección 8 con TPs.
- **Documentación**: `CLAUDE.md` (estructura de carpetas + Template C ahora apunta a M8), `DESIGN.md` (árbol de carpetas), `.claude/skills/ingest/SKILL.md` (TP target path).
- **Verificación**: `0` referencias rotas, `0` huérfanas en M7 y M8.
- **Mentioned-in-history-only**: `log.md` mantiene los paths históricos (`wiki/7. ROS2 y TPs/Img/`) en las entradas de fases pasadas — son registros append-only de lo que se hizo en cada commit, no se reescriben.

## [2026-04-27] ingest | M7 Fase 6 — TP1 + TP2 + TP3 (cierre del módulo)
- **Fuentes**:
  - `Raw/TPs/Enunciado TP1.pdf` (3 págs) — Transformaciones, Locomoción, Sensado
  - `Raw/TPs/Enunciado TP2.pdf` (4 págs) — Modelos Probabilísticos y Filtros Discretos
  - `Raw/TPs/Enunciado TP3.pdf` (5 págs) — Filtros de Partículas
- **Páginas reescritas (Template C de CLAUDE.md)**:
  - `TP1 - Transformaciones, Locomoción y Sensado.md`: 4 ejercicios de matrices T/T12, sensado real con `odom.csv` + `scan.pkl` + matriz `robotTlidar`, secuencia de 7 comandos `diffdrive` para graficar trayectoria.
  - `TP2 - Modelos Probabilísticos y Filtros Discretos.md`: 3 muestreadores (suma de 12, rechazo, Box-Muller) + modelo odometría con ruido evaluado a 5000 muestras + nodo ROS2 con visualización en RVIZ + filtro discreto sobre mundo 1D acotado de 20 celdas.
  - `TP3 - Filtros de Partículas.md`: MCL completo — likelihood field desde OccupancyGrid (con tip de `distance_transform_edt`), `sample_motion_model_odometry` por partícula, `update_particles` con scan + grid, resampleo con deepcopy, log-likelihood trick para underflow.
- **Screenshots**: 4 PNGs nuevas (1 TP2 + 3 TP3 mostrando los hitos del rviz). 109 totales en M7.
- **Meta-updates**:
  - `Robotica.md`: M7 `En progreso (15/21) → Completo ✅ (21/21)`. Catálogo: descripciones completas para TP1/TP2/TP3, **borradas las duplicaciones del bootstrap** (Tutorial 3–8 aparecían dos veces — una con descripción nueva y otra con la del bootstrap original).
  - `wiki/7. ROS2 y TPs/ROS2 y TPs.md` (overview): Fase 6 ✅. **M7 cerrado**.
- **Decisiones tomadas autónomamente**:
  - Por mantener el plan acotado, **no** agregué backlinks en cada concept page que el TP usa (per spec strict del skill `ingest`). En cambio, cada TP enumera las páginas que necesita en su sección "Conceptos que necesitás" y "Conexiones" — el grafo bidireccional se cierra desde el lado del TP. Si en el futuro hace falta hacer el otro sentido (ej. `[[Filtros Discretos]]` mencione `[[TP2]]`), es un lint pass dedicado.
  - Capturé sólo los visuales realmente útiles (4 PNGs): el rviz con partículas de TP2 y los 3 estados del rviz de TP3 (sin likelihood field → con likelihood field → con partículas dispersas). Las matrices y secuencias de TP1 quedaron en LaTeX/markdown nativo, no como screenshot.
  - Cleanup del catálogo: aproveché para borrar las duplicaciones de Tutorial 3–8 que se acumularon en `Robotica.md` durante las fases 2–5.

## [2026-04-27] ingest | M7 Fase 5 — Tutorial 7 + Tutorial 8 (MCL + Kalman family)
- **Fuentes**:
  - `Raw/Diapositivas/Tutoriales/Tutorial 7_ Filtro de Partículas.pdf` (74 slides)
  - `Raw/Diapositivas/Tutoriales/Tutorial 8_ KF, EKF, UKF.pdf` (79 slides)
- **Páginas reescritas**:
  - `Tutorial 7 - Filtro de Partículas.md` (esqueleto → completo): rewrite organizado en 8 secciones — definición conceptual del filtro no paramétrico, las tres fases (predicción/actualización/remuestreo), predicción detallada con sample_motion_model, actualización con ejemplo landmarks (gaussiana sobre $z - \check{z}$), normalización, remuestreo (thresholding vs Stochastic Universal Sampling con ejemplo paso a paso completo), preview de features y data association.
  - `Tutorial 8 - KF, EKF, UKF.md` (esqueleto → completo): rewrite organizado en 7 secciones — truco numérico log-likelihoods + max-shift para evitar underflow en MCL, KF visión general (predict/correct loop), modelo lineal-gaussiano y derivación paso a paso del predict desde $x_t = A_t x_{t-1} + B_t u_t + \varepsilon_t$, algoritmo KF + derivación update via producto de gaussianas, EKF con Taylor + Jacobianos + caso de robot 2D donde la linealización falla catastróficamente (scatter circular), UKF con sigma points + tabla comparativa EKF vs UKF, TP4 con dos arquitecturas (odometría rápida vs paralelo).
- **Screenshots**: 31 PNGs nuevas en `wiki/7. ROS2 y TPs/Img/` (105 totales en M7) — 14 de T7, 17 de T8.
- **Meta-updates**:
  - `Robotica.md`: M7 `En progreso (13/21) → En progreso (15/21)`. Catálogo: descripciones de Tutorial 7 y Tutorial 8.
  - `wiki/7. ROS2 y TPs/ROS2 y TPs.md` (overview): Fase 5 ✅. **Los 8 tutoriales del módulo están completos** — sólo quedan los TPs.
- **Decisiones tomadas autónomamente**:
  - Las slides 9–28 de T7 sobre features/landmarks (extracción del lidar, ICP, nearest neighbour) se mantuvieron como sección "preview" dentro del tutorial — no merecen página propia porque el TP3 usa likelihood field, no features explícitas.
  - El truco log-likelihoods de T8 (slides 4–15, técnico para MCL) quedó como sección dentro de Tutorial 8, sin crear página propia — es un truco de implementación numérica.
  - La derivación paso a paso del KF predict (slides 21–50 de T8) se preservó en el tutorial pedagógicamente — aunque la teoría completa esté en `[[Filtro de Kalman]]` de M5, esta derivación didáctica ayuda a entender de dónde salen las matrices A, Q.
  - El caso del robot 2D con yaw incierto (slides 64–75 de T8) se incluyó completo porque expone el modo de falla del EKF — es lo más valioso del tutorial.

## [2026-04-27] ingest | M7 Fase 4 — Tutorial 6 (Movimiento, Sensores, Histogramas)
- **Fuente**: `Raw/Diapositivas/Tutoriales/Tutorial 6_ Movimiento, Sensores e Histogramas.pdf` (36 páginas, 37 slides — slide 7 saltado en numeración)
- **Página reescrita**:
  - `Tutorial 6 - Movimiento, Sensores e Histogramas.md` (esqueleto → completo): rewrite organizado en 3 secciones — modelo de movimiento con odometría (deltas $\delta_{rot1}, \delta_{trans}, \delta_{rot2}$, ruido, `sample_motion_model_odometry` paso a paso), modelo de sensor (mezcla beam-based de 4 componentes + algoritmo Bresenham para ray-casting con visualización paso a paso + likelihood field como alternativa precomputada), y discretización del espacio (filtro discreto, modelo de movimiento 25/50/25 del TP2).
- **Screenshots**: 15 PNGs nuevas en `wiki/7. ROS2 y TPs/Img/` (74 totales en M7).
  - Recovery: hubo offset de página por slide saltado (slide 7 no existe). Re-extraídas todas las páginas con mapping corregido (slide N para N≥8 = página N-1).
- **Meta-updates**:
  - `Robotica.md`: M7 `En progreso (12/21) → En progreso (13/21)`. Catálogo: descripción de Tutorial 6.
  - `wiki/7. ROS2 y TPs/ROS2 y TPs.md` (overview): Fase 4 ✅.
- **Decisiones tomadas autónomamente**:
  - **Bresenham** se mantuvo como sección dentro del Tutorial 6 (no merece página propia: algoritmo gráfico clásico, no concepto teórico de la materia, su uso en robótica es justo el ray-casting que aparece en el modelo basado en haz).
  - Las secciones del modelo de odometría y del beam/likelihood enlazan a `[[Odometría y Modelo de Movimiento (Odometría)]]` (M2), `[[Modelo de Sensor Basado en Haz]]` y `[[Modelo de Campo de Verosimilitud]]` (M3) en vez de duplicar la teoría.
  - El modelo discreto 25/50/25 quedó como preview de TP2 dentro del Tutorial 6 (cuando se ingeste el enunciado del TP2 en Fase 6, se referenciará desde ahí).

## [2026-04-27] ingest | M7 Fase 3 — Tutorial 5 (Bayes aplicado)
- **Fuente**: `Raw/Diapositivas/Tutoriales/Tutorial 5_ Bayes.pdf` (39 slides)
- **Página reescrita**:
  - `Tutorial 5 - Bayes.md` (esqueleto → completo): rewrite completo organizado en 6 secciones — enunciado del problema (sensor defectuoso, prior 1%), N=1 paso a paso (Bayes + descomposición de evidencia + cálculo numérico → 0.0294), N=2 con prior actualizado (→ 0.083), generalización recursiva con código Python + plot del posterior vs N (sube de 0.03 a ~1.0 en 10 iteraciones), fusión de sensores (luz + GPS) y derivación final del filtro de Bayes que cierra el tutorial.
- **Screenshots**: 9 PNGs nuevas en `wiki/7. ROS2 y TPs/Img/` (59 totales en M7).
- **Meta-updates**:
  - `Robotica.md`: M7 `En progreso (11/21) → En progreso (12/21)`. Catálogo: descripción de Tutorial 5.
  - `wiki/7. ROS2 y TPs/ROS2 y TPs.md` (overview): Fase 3 ✅.
- **Decisiones tomadas autónomamente** (per memoria de autonomía):
  - El ejercicio del sensor defectuoso queda en Tutorial 5 — **NO** se replica en `[[Regla de Bayes]]` (M4) que ya tiene su ejemplo de la puerta. Tutorial 5 es donde vive el worked example completo con código + plot.
  - Las slides 29–39 (rederivación del filtro) overlapean con `[[Filtro de Bayes]]` (M4). En lugar de duplicar, Tutorial 5 las menciona y enlaza a M4 (sección de Derivación).
  - Fusión de sensores quedó como sección dentro del tutorial (no merece página propia: 2 slides, ejemplo simple de Bayes secuencial).

## [2026-04-27] ingest | M7 Fase 2 — Tutorial 3 + Tutorial 4 (TF2, Launch, RVIZ)
- **Fuentes**:
  - `Raw/Diapositivas/Tutoriales/Tutorial 3_ Interactuando con Gazebo.pdf` (33 slides)
  - `Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf` (31 slides)
- **Páginas reescritas en M7**:
  - `ROS2 - TF2.md` (esqueleto → completo): T3 slides 7–14 — frames mapa/robot/sensor, dinámicas vs. estáticas, árbol con `view_frames`, topics `/tf` + `/tf_static`, API `Buffer + TransformListener`.
  - `ROS2 - Launch Files.md` (esqueleto → completo): T4 slides 19–23 — `generate_launch_description()`, `Node(...)`, package.xml `<exec_depend>ros2launch</exec_depend>`, setup.py `data_files`, build + run.
  - `Gazebo y rviz.md` (en-progreso → completo): T1 slides 27–28 + T3 slide 2 + T4 slides 3–8 — Gazebo en detalle, RVIZ inicializar, displays, status OK/Error, views + frames.
  - `Tutorial 3 - Interactuando con Gazebo.md` (esqueleto → completo): rewrite con rqt_graph, TF intro, comandos `topic list/echo/pub`, teleop_keyboard, código `Commander` (timer aleatorio) + `Recorder` (subscribe + atexit).
  - `Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2.md` (esqueleto → completo): rewrite con RVIZ detallado, composición jerárquica de mensajes, launch files, preview TP1 (LIDAR transformado, recorrido) + TP2 (muestreo, modelo odométrico, filtro discreto).
- **Páginas editadas en M7**:
  - `ROS2 - Mensajes.md` — sección nueva "Composición jerárquica" con desglose de Odometry, Pose, Point, Quaternion, Path, PoseStamped (T4 slides 10–17).
- **Screenshots capturadas**: 27 PNGs en `wiki/7. ROS2 y TPs/Img/` (50 totales en M7 ahora).
- **Meta-updates**:
  - `Robotica.md`: M7 `En progreso (8/21) → En progreso (11/21)`. Catálogo M7: descripciones expandidas para 4 páginas (TF2, Launch Files, Gazebo y rviz, Mensajes) + 2 tutoriales.
  - `wiki/7. ROS2 y TPs/ROS2 y TPs.md` (overview): Fase 2 marcada ✅, descripciones de T3/T4 e infra concepts ampliadas.
- **Decisiones tomadas autónomamente** (per memoria de autonomía):
  - `rosbag` no merece página propia — se menciona inline en Tutorial 3 como alternativa al `Recorder + atexit`.
  - URDF / `robot_description` no tiene página — aparecen como "comp existente" en Gazebo y TF2 sin profundizar (se agregaría en una fase futura si surge necesidad).
  - El preview TP1/TP2 se mantuvo en `Tutorial 4` (no se vuelca a las páginas de TPs todavía — eso se ataca en Fase 6 cuando se ingieran los enunciados).

## [2026-04-27] ingest | M7 Fase 1 — Tutorial 1 + Tutorial 2 + ROS2 infra
- **Fuentes**:
  - `Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf` (46 slides)
  - `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf` (63 slides)
- **Páginas reescritas en M7 (8 con contenido grueso)**:
  - `ROS2 - Conceptos Base.md` (T1 slides 11–28: por qué ROS, qué es ROS, stack del curso)
  - `ROS2 - Nodos.md` (T2 slides 4–20: el problema del `while True`, patrón wall_timer + callback)
  - `ROS2 - Topics.md` (T1 slide 20 + T2 slides 28–32: modelo pub/sub, topics típicos del curso)
  - `ROS2 - Mensajes.md` (T2 slides 13, 34: tipos de mensaje, paquetes std_msgs/geometry_msgs/etc.)
  - `ROS2 - Publisher.md` (T2 slides 13–19: API + patrón timer + callback)
  - `ROS2 - Subscriber.md` (T2 slides 31–39: callback reactivo, no usa timer, queue_size)
  - `Gazebo y rviz.md` (T1 slides 27–28: intro a la dupla simulador + visualizador; queda `en-progreso`)
  - `Tutorial 1 - Bienvenida e Instalación de ROS2.md` (rewrite completo: por qué ROS, stack, instalación Robostack/crudo por OS)
  - `Tutorial 2 - Speaker and Listener.md` (rewrite completo: workspace + paquete, talker, listener, correr ambos, anticipo de TP1)
- **Esqueleto en M7 (2, con `[!warning] Pendiente`)**:
  - `ROS2 - TF2.md` (pendiente T3 en Fase 2)
  - `ROS2 - Launch Files.md` (pendiente T4 en Fase 2)
- **Página nueva en M1**:
  - `Cuaterniones.md` (T2 slides 41–60: por qué los cuaterniones, idea geométrica, PRO/CON, workflow Euler↔cuaternión, conexión con ROS2/TF2 y TP1)
- **Páginas editadas en M1**:
  - `Rotaciones.md` — sección nueva "Representaciones de orientación 3D" con tabla comparativa y link a `[[Cuaterniones]]`
  - `Fundamentos.md` (overview) — agregado Cuaterniones al recorrido y lista
- **Screenshots capturadas**: 23 PNGs en `wiki/7. ROS2 y TPs/Img/` + 8 nuevas en `wiki/1. Fundamentos/Img/` (31 total).
  - Recovery: el primer batch de M1 tuvo offset de 2-3 páginas (build slides en T2 corren la numeración). Re-extraídas pages 48, 51, 52, 60; renombradas las correctas.
- **Meta-updates**:
  - `Robotica.md`: M1 `Completo (8/8) → Completo (9/9)`. M7 `Esqueleto (0/21) → En progreso (8/21)`. Catálogo de M1 (+Cuaterniones) y M7 (descripciones expandidas).
  - `wiki/7. ROS2 y TPs/ROS2 y TPs.md` (overview): tabla de fases, marcas de ✅ y `[esqueleto]`.
- **Forward-references** que resolverán en fases siguientes: `[[Tutorial 3-8]]`, `[[TP1-3]]`, contenido detallado de TF2 y Launch Files.
- **Decisiones tomadas autónomamente** (per memoria de autonomía):
  - Cuaterniones a M1 (no a M7) — encaja con Rotaciones/Transformaciones Homogéneas, es teoría pura.
  - Rotaciones recibió sólo una sección extra, no rewrite completo.
  - TF2 y Launch Files quedan esqueleto con callout `[!warning] Pendiente — se completa con Tutorial X (Fase 2)`.

## [2026-04-27] lint | consolidación M4 Robótica Probabilística
- **Diagnóstico**: tras la doble ingesta de M4 (ingesta del 26-04 con páginas combinadas + bootstrap con esqueletos atómicos) coexistían 14 .md, 11 referencias a imágenes inexistentes (`proba - X.png`) y 16 PNGs huérfanas en `Img/`. El catálogo de [[Robotica]] listaba 11/11 atómicas con `Modelo de Sensor` `[esqueleto]`.
- **Migración de contenido**:
  - `Probabilidad - Axiomas y Variables Aleatorias.md` → distribuida entre `Axiomas de Probabilidad`, `Variables Aleatorias Discretas`, `Variables Aleatorias Continuas`, `Probabilidad Conjunta`, `Probabilidad Condicional`.
  - `Actualización Recursiva Bayesiana.md` → fusionada en `Regla de Bayes` (sección "Actualización recursiva: combinando evidencia").
  - `Modelado de Acciones.md` → fusionada en `Modelo de Movimiento` (rewrite completo con ejemplo FSM "cerrar puerta", integración del belief, anticipo del filtro).
- **Imágenes**: re-mapeadas 9 referencias rotas a sus archivos reales (`Bayes - formula.png`, `Markov - DBN.png`, `Filtro Bayes - estructura.png`, etc.). Renombrado `proba-p1-slide-6-06.png` → `VA discretas - PMF.png`. Aprovechadas 4 huérfanas valiosas (`Bayes - causal vs diagnostico`, `Bayes - puerta abierta`, `Bayes - ejemplo numerico puerta`, `Localizacion probabilistica - pasos`). Borradas 6 raw `proba-p1-slide-*.png` duplicadas.
- **`Modelo de Sensor`** completado como hub explícito a M3 (se documenta su rol como mitad del filtro de Bayes y se enumeran las tres familias de modelos).
- **Eliminadas** las 3 páginas viejas con wikilinks rotos (`[[_Overview]]`, `[[Home]]`).
- **Estado final M4**: 12 .md (1 overview + 11 conceptos), 21 imágenes referenciadas, **0 referencias rotas, 0 huérfanas**. Catálogo `Robotica.md`: M4 `En progreso (10/11)` → `Completo (11/11)`.

## [2026-04-26] bootstrap | construcción del grafo de conceptos completos (M4–M7)
- **Objetivo**: crear esqueletos de todas las páginas principales extraídas de `Raw/` y establecer el grafo de enlaces cruzados.
- Páginas creadas M1 (2):
  - `Introducción a la Robótica Móvil.md` (fuente: 00-introduccion)
  - `Paradigmas de Control de Robots.md` (fuente: 02-paradigmas)
- Páginas creadas M4 (11):
  - `Introducción a la Robótica Probabilística.md`, `Axiomas de Probabilidad.md`, `Variables Aleatorias Discretas.md`, `Variables Aleatorias Continuas.md`, `Probabilidad Conjunta.md`, `Probabilidad Condicional.md`, `Regla de Bayes.md`, `Suposición de Markov.md`, `Filtro de Bayes.md`, `Modelo de Movimiento.md`, `Modelo de Sensor.md`
- Páginas creadas M5 (9):
  - `Filtros Discretos.md`, `MCL - Filtro de Partículas.md`, `Muestreo por Importancia.md`, `Gaussiana Multivariada.md`, `Filtro de Kalman.md`, `EKF - Filtro de Kalman Extendido.md`, `UKF - Filtro de Kalman No Centrado.md`, `Puntos Sigma.md`, `Descomposición de Cholesky.md`
- Páginas creadas M6 (6):
  - `Mapas de Ocupación.md`, `Mapas de Grilla.md`, `Mapeo con Poses Conocidas.md`, `SLAM - Mapeo y Localización Simultánea.md`, `Features vs Mapas Volumétricos.md`, `Exploración.md`
- Páginas creadas M7 (21):
  - ROS2 infra: `ROS2 - Conceptos Base.md`, `ROS2 - Nodos.md`, `ROS2 - Topics.md`, `ROS2 - Mensajes.md`, `ROS2 - Publisher.md`, `ROS2 - Subscriber.md`, `ROS2 - TF2.md`, `ROS2 - Launch Files.md`, `Gazebo y rviz.md`
  - Tutoriales: T1–T8 (8 páginas)
  - TPs: `TP1 - Transformaciones, Locomoción y Sensado.md`, `TP2 - Modelos Probabilísticos y Filtros Discretos.md`, `TP3 - Filtros de Partículas.md`
- **Total páginas creadas**: 49
- Actualizados:
  - `_Overview.md` de M1, M4, M5, M6, M7 (taglines, narrativa, recorrido sugerido, conexiones, lista de páginas)
  - `Robotica.md` (entradas para 49 páginas nuevas, 5 módulos actualizados)
  - `Robotica.md` (M4–M7: Esqueleto → En progreso)
- **Próximo paso**: ingesta de contenido grounded por PDF usando la skill `ingest`.
- Plan-mode-friendly por construcción: fases 1–3 read-only para el plan window, phase 4 es el ExitPlanMode gate, fases 5–6 ejecutan tras aprobación.

## [2026-04-27] consulta | Derivación del Filtro de Bayes
- Usuario pidió derivación completa de las ecuaciones de predicción y actualización
- Explicación en chat: Bayes → Markov → probabilidad total → ecuaciones finales
- Creada nueva página `wiki/4. Robótica Probabilística/Derivación del Filtro de Bayes.md`
- Actualizado catálogo en `Robotica.md`
