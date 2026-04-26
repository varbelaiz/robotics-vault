> [[Home|← Inicio]]

# Módulo 4 — Robótica Probabilística

> Representar la incertidumbre **explícitamente** con probabilidad, y propagarla en el tiempo combinando observaciones (Bayes) y acciones — la maquinaria que construye los filtros de Kalman, partículas e histogramas del módulo 5.

## Por qué este módulo
Los robots ven el mundo a través de sensores ruidosos y actúan con motores imperfectos. La pregunta central es: dado todo lo que el robot observó y todas las acciones que ejecutó, ¿qué cree saber sobre su estado actual? Este módulo construye, paso a paso, la respuesta canónica: el **filtro de Bayes**, que es la base de todos los algoritmos de localización del módulo 5.

## Recorrido sugerido
1. [[Probabilidad - Axiomas y Variables Aleatorias]] — repaso del lenguaje base (axiomas, conjunta, condicional, marginalización).
2. [[Regla de Bayes]] — cómo invertir una probabilidad condicional, con el ejemplo canónico de la puerta.
3. [[Actualización Recursiva Bayesiana]] — cómo combinar varias observaciones con la suposición de Markov.
4. [[Modelado de Acciones]] — cómo incorporar las acciones del robot, $P(x \mid u, x')$.
5. [[Filtro de Bayes]] — derivación, algoritmo, ejemplo de localización 1D.

## Conecta con
- ⬅️ [[1. Fundamentos/_Overview|Módulo 1: Fundamentos]] (matrices, gaussianas)
- ⬅️ [[2. Locomoción/_Overview|Módulo 2: Locomoción]] (los modelos de movimiento son instancias de $P(x \mid u, x')$)
- ⬅️ [[3. Sensores y Modelos/_Overview|Módulo 3: Sensores]] (los modelos de sensor son instancias de $P(z \mid x)$)
- ➡️ [[5. Filtros Bayesianos/_Overview|Módulo 5: Filtros Bayesianos]] (Kalman, partículas y histogramas son instancias del filtro de Bayes)
- ➡️ [[6. Mapeo/_Overview|Módulo 6: Mapeo]] (los mapas de ocupación se actualizan con un filtro de Bayes por celda)

## Páginas en este módulo
- [[Probabilidad - Axiomas y Variables Aleatorias]]
- [[Regla de Bayes]]
- [[Actualización Recursiva Bayesiana]]
- [[Modelado de Acciones]]
- [[Filtro de Bayes]]
