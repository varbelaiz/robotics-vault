# Diseño del Wiki de Robótica

> Documento de decisiones de diseño, alternativas consideradas y rationale.
> Brainstorming completado el 2026-04-26.

## Contexto

Vault de Obsidian para la materia universitaria de **Robótica**. El material en `Raw/` cubre tres capas:

- **14 PDFs teóricos** (`Diapositivas/Teoricas/`, numeradas 00–12): introducción, álgebra lineal, paradigmas, locomoción, sensores, robótica probabilística (parte 1 y 2), modelos de movimiento, modelos de sensores, filtros discretos, filtro de partículas (MCL), filtro de Kalman, EKF, UKF y mapas de ocupación.
- **8 tutoriales de ROS2** (`Diapositivas/Tutoriales/`, T1–T8): instalación, speaker/listener, Gazebo, msgs/rviz/launch, Bayes, movimiento + sensores + histogramas, filtro de partículas, KF/EKF/UKF.
- **Material complementario**: enunciados de TP1, TP2, TP3 y un *Resumen Parcial* armado por un compañero.

**Todo entra en el parcial** (próximas semanas). El usuario está perdido en la materia y necesita una base sólida desde la cual estudiar. El vault tiene que servir tanto para el parcial inmediato como para sostener estudio futuro (final, materias relacionadas).

## Objetivo

Construir un knowledge base estilo *LLM Wiki* (patrón Karpathy): páginas conceptuales interconectadas que el LLM genera y mantiene, ancladas estrictamente al material en `Raw/`. El usuario cura fuentes, hace preguntas y dirige el análisis; el LLM hace todo el trabajo mecánico (resumir, cross-referenciar, mantener consistencia).

## Decisiones de diseño

| # | Decisión | Elegido | Alternativas descartadas |
|---|---|---|---|
| 1 | Estructura organizativa | **Híbrido por módulos temáticos** (7 carpetas) | (a) Wiki conceptual plano puro Karpathy: rechazado por carga cognitiva alta para alguien perdido. (b) Patrón NLP (carpeta por clase): rechazado porque las slides de Robótica no traen preguntas que orienten el estudio. (c) Outcome-driven (sólo guía de parcial): rechazado por no capitalizar más allá del parcial. |
| 2 | Integración de capas (teoría / ROS2 / TPs) | **Teoría como base, práctica como anexo** | (a) Páginas separadas por capa. (c) Páginas unificadas que mezclan teoría + tutorial + TP. |
| 3 | Profundidad matemática | **Intuición + derivación completa en la misma página** | (a) Intuición + fórmula como referencia, derivaciones en apéndice. (c) Sólo lo que la cátedra evalúa. |
| 4 | Grounding | **Estricto** — toda sección con cita a slide específica, cero invención | Síntesis libre (rechazada por preferencia explícita del usuario). |
| 5 | Política de screenshots | **Liberal** — 6–10 por página, fórmulas / diagramas / definiciones / ejemplos / fotos | Conservadora (3 máx., sólo diagramas) — relajada explícitamente. |
| 6 | Workflow de ingesta | **Una fuente a la vez con usuario en el loop** | Batch sin supervisión (queda como opción cuando el usuario esté cómodo). |
| 7 | Skill de screenshots | **`slide-screenshot` adaptada** desde el vault de NLP | Capturar manualmente con `pdftoppm`. |

## Estructura resultante

```
Robotica/
├── Raw/                              ← fuentes inmutables, nunca tocadas
│   ├── Diapositivas/{Teoricas,Tutoriales}/
│   ├── Apuntes/
│   └── TPs/
├── wiki/                             ← contenido owned por el LLM
│   ├── 1. Fundamentos/
│   ├── 2. Locomoción/
│   ├── 3. Sensores y Modelos/
│   ├── 4. Robótica Probabilística/
│   ├── 5. Filtros Bayesianos/
│   ├── 6. Mapeo/
│   ├── 7. ROS2/                       ← framework + 8 tutoriales
│   └── 8. TPs/                        ← trabajos prácticos
│       Cada carpeta: _Overview.md + <Concepto>.md + Img/
├── Robotica.md                           ← entry point + tabla de estado
├── Robotica.md                          ← catálogo plano de todas las páginas
├── log.md                            ← bitácora append-only
├── CLAUDE.md                         ← schema operativo
├── DESIGN.md                         ← este documento
└── .claude/skills/slide-screenshot/SKILL.md
```

## Anatomía de páginas

Cuatro templates (detalle completo en `CLAUDE.md`):

- **A — Concept page** (80% del wiki): frontmatter YAML + back-link + prerequisitos + intuición + modelo + algoritmo + derivación + variantes + (en ROS2/TPs) + fuentes. Cada sección con cita inline al material fuente.
- **B — `_Overview.md`** por módulo: narrativa del módulo + recorrido sugerido + conexiones con otros módulos.
- **C — Página de TP**: objetivo + conceptos que usa + pasos del enunciado + notas de implementación.
- **D — Tutorial ROS2**: como A pero con énfasis en código (`bash`, `python`, `xml`).

## Topología del grafo

- **Hubs conceptuales** (alto in-degree, prioridad alta de escritura): `Regla de Bayes`, `Probabilidad - Repaso`, `Algebra Lineal`, `Filtro de Kalman`, `Modelo de Movimiento`, `Modelo de Sensor`. Sostienen al resto de las páginas.
- **Cross-module bridges críticos**: M1 (álgebra) y M4 (probabilidad) alimentan M5 (filtros). M5 alimenta M6 (mapeo). M7 (TPs/ROS2) cita conceptos de M1–M6.
- **Aristas semánticas** modeladas: prerequisitos, variantes/generalizaciones, "usado en TP", "implementado en tutorial", citas a fuentes.
- **`Robotica.md` y `log.md` quedan fuera del grafo conceptual** — son meta files, no contaminan el graph view de Obsidian.

## Workflows

- **Ingesta**: leer PDF → discutir takeaways → proponer plan de cambios → esperar aprobación → ejecutar (concepto + `_Overview` + `Home` + `index` + `log`) → resumen final.
- **Consulta**: leer `index` → drill-down a páginas relevantes → responder con wikilinks y citas → ofrecer file-back al wiki si la respuesta es valiosa → log.
- **Lint** (on demand): huérfanos, links rotos, secciones sin grounding, citas a páginas inexistentes, imágenes huérfanas, estado desincronizado, frontmatter inconsistente, contradicciones, sugerencias proactivas.

## Bootstrap

El plan de implementación detalla la secuencia exacta. A grandes rasgos:

1. Meta files raíz (`Home`, `index`, `log`, `CLAUDE.md`, `.gitignore`).
2. Esqueletos de los 7 módulos (`_Overview.md` mínimo + `Img/` vacío).
3. Adaptación de la skill `slide-screenshot` (cambios: trigger, fuentes en `Raw/`, carpeta de salida `Img/` por módulo, cap relajado a 6–10).
4. Eliminar `Welcome.md` placeholder.
5. `git init` + commit inicial + remoto privado en `varbelaiz/robotics-vault`.
6. Primera ingesta piloto (orden a definir por el usuario).

## Cuestiones abiertas / a decidir más adelante

- **Primera ingesta piloto**: orden no decidido. Sugerencias: (a) `Teóricas/00-introduccion` + `01-algebra_lineal` para panorama; (b) `Teóricas/05-intro_robo_proba` para atacar el corazón del parcial; (c) un Tutorial corto primero para validar el flow end-to-end.
- **Plugin Dataview de Obsidian**: si el wiki crece, valdría usarlo para listas autogeneradas por módulo desde frontmatter.
- **Tooling de búsqueda**: `Robotica.md` alcanza a escala media (~50 páginas). A escala mayor, evaluar `qmd` u otra solución local.
- **Profundidad de páginas de TP**: el template C cubre objetivo + conceptos + pasos. Si se quiere registrar también la *resolución* paso-a-paso, agregar sección `## Resolución` o página separada.
- **Marp / slide decks**: opcional para repaso pre-parcial. Postergado.
