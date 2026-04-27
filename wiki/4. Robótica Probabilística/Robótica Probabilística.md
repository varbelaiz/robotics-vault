> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Módulo 4 — Robótica Probabilística

> Fundamentos probabilísticos: axiomas, variables aleatorias, Bayes, Markov, y el filtro que alimenta toda la localización y el mapeo.

## Por qué este módulo
Todo el curso se apoya en representar y actualizar creencias bajo incertidumbre. Este módulo proporciona el lenguaje formal: [[Regla de Bayes|Bayes]], [[Suposición de Markov|Markov]], y el [[Filtro de Bayes]] como algoritmo central. Además, introduce los dos ingredientes que necesitan los filtros: [[Modelo de Movimiento]] y [[Modelo de Sensor]].

## Recorrido sugerido
1. [[Introducción a la Robótica Probabilística]] — idea general de incertidumbre explícita.
2. [[Axiomas de Probabilidad]] — fundamentos formales.
3. [[Variables Aleatorias Discretas]] — espacios de estados discretos.
4. [[Variables Aleatorias Continuas]] — densidades en espacios continuos.
5. [[Probabilidad Conjunta]] — distribuciones multivariadas.
6. [[Probabilidad Condicional]] — condicionamiento y reglas.
7. [[Regla de Bayes]] — actualización con evidencia.
8. [[Suposición de Markov]] — dependencia temporal local.
9. [[Filtro de Bayes]] — algoritmo recursivo de estimación.
10. [[Derivación del Filtro de Bayes]] — paso a paso: Bayes + Markov + probabilidad total → predicción + actualización.
11. [[Modelo de Movimiento]] — $P(x_t \mid u_t, x_{t-1})$.
12. [[Modelo de Sensor]] — $P(z_t \mid x_t, m)$.

## Conecta con
- ⬅️ [[1. Fundamentos/Fundamentos|Módulo 1: Fundamentos]] (prerequisito matemático)
- ➡️ [[5. Filtros Bayesianos/Filtros Bayesianos|Módulo 5: Filtros Bayesianos]] (implementaciones del filtro: discretos, Kalman, partículas)
- ➡️ [[6. Mapeo/Mapeo|Módulo 6: Mapeo]] (mapeo probabilístico)

## Páginas en este módulo
- [[Introducción a la Robótica Probabilística]]
- [[Axiomas de Probabilidad]]
- [[Variables Aleatorias Discretas]]
- [[Variables Aleatorias Continuas]]
- [[Probabilidad Conjunta]]
- [[Probabilidad Condicional]]
- [[Regla de Bayes]]
- [[Suposición de Markov]]
- [[Filtro de Bayes]]
- [[Derivación del Filtro de Bayes]]
- [[Modelo de Movimiento]]
- [[Modelo de Sensor]]
