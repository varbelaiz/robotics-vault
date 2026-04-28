> [[Locomoción|← Locomoción]] | [[Robotica|← Inicio]]

# Módulo 2 — Locomoción

> Cómo se mueve un robot: cinemática de los accionamientos con ruedas y odometría como integración temporal del movimiento.

## Por qué este módulo
La cinemática del robot es la base operativa de todo lo que viene: define cómo traducir un comando $u$ (velocidad lineal/angular o velocidades de rueda) a un cambio en la pose $x$. La odometría integra esa cinemática en el tiempo a partir de los encoders y produce una estimación cruda de pose — entrada del modelo probabilístico que la consume en M4.

> [!info] Modelos probabilísticos: en M4
> Los **modelos probabilísticos de movimiento** (basados en odometría y en velocidad) y la maquinaria de muestreo viven ahora en el [[4. Robótica Probabilística/Robótica Probabilística|módulo 4]]. Esa separación respeta el orden cronológico de las clases: este módulo cubre la slide 03 (locomoción + odometría como concepto), y los modelos probabilísticos cubren la slide 06 (después de la intro probabilística de la slide 05).

## Recorrido sugerido
1. [[Locomoción y Tipos de Accionamientos]] — el panorama: tipos de locomoción, accionamientos con ruedas, restricciones holonómicas y no-holonómicas.
2. [[Cinemática del Robot Diferencial]] — derivación completa del accionamiento más usado, con ICC.
3. [[Otros Accionamientos - Ackermann, Síncrono, Omni]] — Ackermann, síncrono, mecanum, vehículos especiales.
4. [[Odometría]] — dead reckoning como integración de la cinemática a partir de encoders.

## Conecta con
- ⬅️ [[1. Fundamentos/Fundamentos|Módulo 1: Fundamentos]] (matrices de rotación, jacobiano, transformaciones)
- ➡️ [[3. Sensores/Sensores|Módulo 3: Sensores]] (los encoders alimentan la odometría)
- ➡️ [[4. Robótica Probabilística/Robótica Probabilística|Módulo 4: Robótica Probabilística]] (modelo $p(x_t \mid u_t, x_{t-1})$ basado en odometría / en velocidad + muestreo)
- ➡️ [[5. Filtros Bayesianos/Filtros Bayesianos|Módulo 5: Filtros Bayesianos]] (los modelos de movimiento alimentan los filtros)
- ➡️ [[7. ROS2/ROS2|Módulo 7: ROS2]] (Tutorial 6 implementa estos modelos en ROS2)

## Páginas en este módulo
- [[Locomoción y Tipos de Accionamientos]]
- [[Cinemática del Robot Diferencial]]
- [[Otros Accionamientos - Ackermann, Síncrono, Omni]]
- [[Odometría]]
