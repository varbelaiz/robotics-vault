> [[Filtros Bayesianos|← Filtros Bayesianos]] | [[Robotica|← Inicio]]

# Módulo 5 — Filtros Bayesianos

> Algoritmos concretos que implementan el [[Filtro de Bayes]]: discretos, partículas, y Kalman (lineal, extendido, no centrado).

## Por qué este módulo
El [[Filtro de Bayes]] del Módulo 4 es el algoritmo abstracto. Este módulo proporciona las implementaciones que funcionan en la práctica, cada una con sus trade-offs de eficiencia, precisión, y complejidad del espacio de estados.

## Recorrido sugerido
1. [[Filtros Discretos]] — grilla, O(n) con convolución.
2. [[MCL - Filtro de Partículas]] — representación con partículas, distribuciones arbitrarias.
3. [[Muestreo por Importancia]] — técnica de muestreo para MCL.
4. [[Gaussiana Multivariada]] — distribución para Kalman.
5. [[Filtro de Kalman]] — óptimo para sistemas lineales gaussianos.
6. [[EKF]] — linealización con Jacobianos.
7. [[UKF]] — sigma points sin Jacobianos.
8. [[Puntos Sigma]] — construcción del conjunto sigma.
9. [[Descomposición de Cholesky]] — raíz cuadrada de matriz para UKF.

## Conecta con
- ⬅️ [[4. Robótica Probabilística/Robótica Probabilística|Módulo 4: Probabilidad]] (filtro de Bayes base, modelos)
- ⬅️ [[1. Fundamentos/Fundamentos|Módulo 1: Fundamentos]] (matrices definidas positivas, Jacobianos)
- ➡️ [[6. Mapeo/Mapeo|Módulo 6: Mapeo]] (localización dentro de SLAM)
- ➡️ [[7. ROS2/ROS2|Módulo 7: ROS2]] (Tutorials 7–8, TP3)

## Páginas en este módulo
- [[Filtros Discretos]]
- [[MCL - Filtro de Partículas]]
- [[Muestreo por Importancia]]
- [[Gaussiana Multivariada]]
- [[Filtro de Kalman]]
- [[EKF]]
- [[UKF]]
- [[Puntos Sigma]]
- [[Descomposición de Cholesky]]
