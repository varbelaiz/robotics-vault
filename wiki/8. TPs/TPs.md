> [[Robotica|← Inicio]]

# Módulo 8 — TPs

> Trabajos prácticos del curso. Cada TP integra varios módulos teóricos en código que corre sobre el simulador del Turtlebot3.

## Por qué este módulo

Los TPs son los **entregables** evaluables del curso. Cada uno tiene un alcance bien delimitado (3–5 páginas de enunciado) pero exige integrar:

- **TP1** — la matemática de [[Transformaciones Homogéneas]] + [[Cinemática del Robot Diferencial]] + lectura del lidar real.
- **TP2** — [[Variables Aleatorias Continuas|muestreo]] + [[Modelo de Movimiento (Odometría)|modelo de odometría]] con ruido + [[Filtros Discretos|filtro de histograma]].
- **TP3** — [[MCL - Filtro de Partículas|MCL completo]] sobre el simulador con [[Modelo de Campo de Verosimilitud|likelihood field]].

## Recorrido sugerido

1. [[TP1 - Transformaciones, Locomoción y Sensado]] — transformaciones 2D, lectura de datos reales, `diffdrive`.
2. [[TP2 - Modelos Probabilísticos y Filtros Discretos]] — 3 muestreadores de gaussiana, modelo de odometría con ruido, filtro discreto 1D.
3. [[TP3 - Filtros de Partículas]] — MCL completo en ROS2 con likelihood field.

## Conecta con

- ⬅️ [[1. Fundamentos/Fundamentos|Módulo 1: Fundamentos]] — TP1 usa [[Transformaciones Homogéneas]], [[Rotaciones]], [[Cuaterniones]].
- ⬅️ [[2. Locomoción/Locomoción|Módulo 2: Locomoción]] — TP1 implementa la [[Cinemática del Robot Diferencial]]; TP2/TP3 usan [[Modelo de Movimiento (Odometría)|modelo de odometría]].
- ⬅️ [[3. Sensores/Sensores|Módulo 3: Sensores]] — TP3 usa [[Modelo de Campo de Verosimilitud]] sobre el lidar.
- ⬅️ [[4. Robótica Probabilística/Robótica Probabilística|Módulo 4: Robótica Probabilística]] — TP2/TP3 son [[Filtro de Bayes]] aplicado.
- ⬅️ [[5. Filtros Bayesianos/Filtros Bayesianos|Módulo 5: Filtros Bayesianos]] — TP2 implementa [[Filtros Discretos]]; TP3 implementa [[MCL - Filtro de Partículas]].
- ⬅️ [[6. Mapeo/Mapeo|Módulo 6: Mapeo]] — TP3 consume un [[Mapas de Ocupación|OccupancyGrid]].
- ⬅️ [[7. ROS2/ROS2|Módulo 7: ROS2]] — todos los TPs corren en ROS2; TP2 y TP3 con paquetes propios (`tp2`, `tp3`).

## Páginas en este módulo

- [[TP1 - Transformaciones, Locomoción y Sensado]]
- [[TP2 - Modelos Probabilísticos y Filtros Discretos]]
- [[TP3 - Filtros de Partículas]]
