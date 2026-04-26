> [[Home|← Inicio]]

# Módulo 2 — Locomoción

> Cómo se mueve un robot: cinemática de los accionamientos con ruedas, integración del movimiento (odometría), y modelado probabilístico para alimentar filtros de Bayes.

## Por qué este módulo
La cinemática del robot es la base operativa de todo lo que viene: define cómo traducir un comando $u$ (velocidad lineal/angular o velocidades de rueda) a un cambio en la pose $x$, y cómo la incertidumbre en ese movimiento se propaga. Sin un modelo de movimiento sano, los filtros bayesianos del módulo 5 no tienen sobre qué construir.

## Recorrido sugerido
1. [[Locomoción y Tipos de Accionamientos]] — el panorama: tipos de locomoción, accionamientos con ruedas, restricciones holonómicas y no-holonómicas.
2. [[Cinemática del Robot Diferencial]] — derivación completa del accionamiento más usado, con ICC.
3. [[Otros Accionamientos - Ackermann, Síncrono, Omni]] — Ackermann, síncrono, mecanum, vehículos especiales.
4. [[Odometría y Modelo de Movimiento (Odometría)]] — dead reckoning + modelo probabilístico basado en encoders.
5. [[Modelo de Movimiento (Velocidad)]] — modelo probabilístico cuando el control viene como $(v, \omega)$.

## Conecta con
- ⬅️ [[1. Fundamentos/_Overview|Módulo 1: Fundamentos]] (matrices de rotación, jacobiano, transformaciones)
- ➡️ [[3. Sensores y Modelos/_Overview|Módulo 3: Sensores]] (los encoders alimentan la odometría)
- ➡️ [[5. Filtros Bayesianos/_Overview|Módulo 5: Filtros Bayesianos]] (los modelos de movimiento alimentan los filtros)
- ➡️ [[7. ROS2 y TPs/_Overview|Módulo 7: ROS2 y TPs]] (Tutorial 6 implementa estos modelos en ROS2)

## Páginas en este módulo
- [[Locomoción y Tipos de Accionamientos]]
- [[Cinemática del Robot Diferencial]]
- [[Otros Accionamientos - Ackermann, Síncrono, Omni]]
- [[Odometría y Modelo de Movimiento (Odometría)]]
- [[Modelo de Movimiento (Velocidad)]]
