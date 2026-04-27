# Bitácora del Wiki

> Append-only. Una entrada por operación.
> Formato: `## [YYYY-MM-DD] {ingest|consulta|lint|bootstrap} | <título corto>`
> Recientes: `grep "^## \[" log.md | tail -10`

> [[Robotica|← Inicio]]

---

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
