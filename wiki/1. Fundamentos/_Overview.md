> [[Home|← Inicio]]

# Módulo 1 — Fundamentos

> La matemática base sobre la que se construye toda la materia: vectores, matrices, transformaciones. Todo lo que un robot **representa** (poses, observaciones, incertidumbre) y **calcula** (filtros, mapas, planificación) está hecho de estos ladrillos.

## Por qué este módulo

La robótica moderna es esencialmente **álgebra lineal aplicada a sistemas con incertidumbre**. Antes de hablar de filtros bayesianos, modelos de movimiento o mapeo, hace falta tener afinados:

- Cómo se representan **posiciones y orientaciones** ([[Vectores]], [[Rotaciones|matrices de rotación]], [[Transformaciones Homogéneas]]).
- Cómo se **transforman entre sistemas de referencia** (mundo ↔ robot ↔ sensor ↔ objeto).
- Las herramientas matemáticas que después aparecen como **piezas centrales en los filtros**: [[Matriz Definida Positiva|matrices definidas positivas]] (covarianzas en Kalman) y [[Matriz Jacobiana|Jacobianas]] (linealización en EKF).

## Recorrido sugerido

1. [[Vectores]] — definición, producto interno, dependencia lineal.
2. [[Matrices]] — operaciones, productos, rango, inversa, traza, determinante.
3. [[Matriz Definida Positiva]] — pieza para covarianzas en filtros.
4. [[Matriz Jacobiana]] — pieza para linealización en EKF.
5. [[Rotaciones]] — ortogonales, 2D, 3D, no conmutatividad.
6. [[Transformaciones Homogéneas]] — rototraslación, pose, composición.

## Conecta con
- ➡️ [[2. Locomoción/_Overview|Módulo 2: Locomoción]] (poses del robot que cambian en el tiempo).
- ➡️ [[3. Sensores y Modelos/_Overview|Módulo 3: Sensores y Modelos]] (transformaciones sensor → robot).
- ➡️ [[5. Filtros Bayesianos/_Overview|Módulo 5: Filtros Bayesianos]] (matrices definidas positivas, Jacobianas).
- ➡️ Es prerequisito de todos los módulos posteriores.

## Páginas en este módulo
- [[Vectores]]
- [[Matrices]]
- [[Matriz Definida Positiva]]
- [[Matriz Jacobiana]]
- [[Rotaciones]]
- [[Transformaciones Homogéneas]]
