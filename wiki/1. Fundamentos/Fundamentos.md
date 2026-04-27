> [[Fundamentos|← Fundamentos]] | [[Robotica|← Inicio]]

# Módulo 1 — Fundamentos

> La matemática base sobre la que se construye toda la materia: vectores, matrices, transformaciones. Todo lo que un robot **representa** (poses, observaciones, incertidumbre) y **calcula** (filtros, mapas, planificación) está hecho de estos ladrillos.

## Por qué este módulo

La robótica moderna es esencialmente **álgebra lineal aplicada a sistemas con incertidumbre**. Antes de hablar de filtros bayesianos, modelos de movimiento o mapeo, hace falta tener afinados:

- Cómo se representan **posiciones y orientaciones** ([[Vectores]], [[Rotaciones|matrices de rotación]], [[Transformaciones Homogéneas]]).
- Cómo se **transforman entre sistemas de referencia** (mundo ↔ robot ↔ sensor ↔ objeto).
- Las herramientas matemáticas que después aparecen como **piezas centrales en los filtros**: [[Matriz Definida Positiva|matrices definidas positivas]] (covarianzas en Kalman) y [[Matriz Jacobiana|Jacobianas]] (linealización en EKF).

## Recorrido sugerido

1. [[Introducción a la Robótica Móvil]] — panorama del campo y problemas fundamentales.
2. [[Paradigmas de Control de Robots]] — evolución: clásico, reactivo, híbrido.
3. [[Vectores]] — definición, producto interno, dependencia lineal.
4. [[Matrices]] — operaciones, productos, rango, inversa, traza, determinante.
5. [[Matriz Definida Positiva]] — pieza para covarianzas en filtros.
6. [[Matriz Jacobiana]] — pieza para linealización en EKF.
7. [[Rotaciones]] — ortogonales, 2D, 3D, no conmutatividad.
8. [[Transformaciones Homogéneas]] — rototraslación, pose, composición.

## Conecta con
- ➡️ [[2. Locomoción/Locomoción|Módulo 2: Locomoción]] (poses del robot que cambian en el tiempo).
- ➡️ [[3. Sensores y Modelos/Sensores y Modelos|Módulo 3: Sensores y Modelos]] (transformaciones sensor → robot).
- ➡️ [[4. Robótica Probabilística/Robótica Probabilística|Módulo 4: Robótica Probabilística]] (incertidumbre modelada).
- ➡️ [[5. Filtros Bayesianos/Filtros Bayesianos|Módulo 5: Filtros Bayesianos]] (matrices definidas positivas, Jacobianas).
- Es prerequisito de todos los módulos posteriores.

## Páginas en este módulo
- [[Introducción a la Robótica Móvil]]
- [[Paradigmas de Control de Robots]]
- [[Vectores]]
- [[Matrices]]
- [[Matriz Definida Positiva]]
- [[Matriz Jacobiana]]
- [[Rotaciones]]
- [[Transformaciones Homogéneas]]
