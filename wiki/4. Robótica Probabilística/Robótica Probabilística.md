> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Módulo 4 — Robótica Probabilística

> Fundamentos probabilísticos: axiomas, variables aleatorias, Bayes, Markov, el filtro de Bayes, y los **modelos probabilísticos de movimiento y de sensor** que lo alimentan.

## Por qué este módulo
Todo el curso se apoya en representar y actualizar creencias bajo incertidumbre. Este módulo proporciona el lenguaje formal: [[Regla de Bayes|Bayes]], [[Suposición de Markov|Markov]], y el [[Filtro de Bayes]] como algoritmo central. Además, contiene los **dos ingredientes concretos** que necesitan los filtros: el [[Modelo de Movimiento]] y el [[Modelo de Sensor]] junto con sus instancias canónicas. Cubre las slides teóricas 05, 06 y 07.

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
11. [[Modelo de Movimiento]] — hub: $P(x_t \mid u_t, x_{t-1})$ con ejemplo discreto (cerrar puerta).
12. [[Modelo de Movimiento (Odometría)]] — instancia con $u$ basado en encoders, parametrización `(rot1, trans, rot2)`.
13. [[Modelo de Movimiento (Velocidad)]] — instancia con $u = (v, \omega)$ y trayectoria circular.
14. [[Muestreo de Distribuciones]] — operaciones `prob` y `sample` (normal/triangular/rejection) que ambos modelos invocan.
15. [[Modelo de Sensor]] — hub: $P(z_t \mid x_t, m)$ y las tres familias canónicas.
16. [[Modelo de Sensor Basado en Haz]] — mezcla hit/unexp/max/rand para sonar y lidar.
17. [[Modelo de Campo de Verosimilitud]] — alternativa eficiente y suave al beam-based.
18. [[Modelo de Detección de Landmarks]] — modelo cuando se observan marcadores conocidos.

## Conecta con
- ⬅️ [[1. Fundamentos/Fundamentos|Módulo 1: Fundamentos]] (prerequisito matemático)
- ⬅️ [[2. Locomoción/Locomoción|Módulo 2: Locomoción]] ([[Odometría]] y cinemática como prerequisito de los modelos de movimiento)
- ⬅️ [[3. Sensores/Sensores|Módulo 3: Sensores]] (hardware que alimenta los modelos de sensor)
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
- [[Modelo de Movimiento (Odometría)]]
- [[Modelo de Movimiento (Velocidad)]]
- [[Muestreo de Distribuciones]]
- [[Modelo de Sensor]]
- [[Modelo de Sensor Basado en Haz]]
- [[Modelo de Campo de Verosimilitud]]
- [[Modelo de Detección de Landmarks]]
