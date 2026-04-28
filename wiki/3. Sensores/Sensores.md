> [[Sensores|← Sensores]] | [[Robotica|← Inicio]]

# Módulo 3 — Sensores

> Qué sensores usa un robot móvil para percibir su estado y su entorno: clasificación, principios de funcionamiento, fuentes de error y tipos de hardware.

## Por qué este módulo
Toda localización y mapeo dependen de medir el entorno, pero los sensores son ruidosos, sesgados, y a veces directamente fallan (cristal, espejos, crosstalk). Este módulo cubre el **hardware**: encoders, IMU, GPS, sonar, lidar, cámaras — qué miden, cómo, con qué errores típicos.

> [!info] Modelos probabilísticos: en M4
> El **modelo probabilístico** $p(z \mid x, m)$ que consume los datos del sensor (beam-based, likelihood field, landmarks) vive en el [[4. Robótica Probabilística/Robótica Probabilística|módulo 4]] junto con el resto del andamiaje probabilístico. Esa separación respeta el orden cronológico: este módulo cubre la slide 04 (sensores hardware) y los modelos cubren la slide 07 (después de la intro probabilística de la slide 05).

## Recorrido sugerido
1. [[Sensores Internos]] — clasificación, encoders, táctiles, IMU, tabla resumen.
2. [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — exteroceptivos a distancia.

## Conecta con
- ⬅️ [[1. Fundamentos/Fundamentos|Módulo 1: Fundamentos]] (matrices, atan2, gaussianas)
- ⬅️ [[2. Locomoción/Locomoción|Módulo 2: Locomoción]] (los encoders alimentan [[Odometría]])
- ➡️ [[4. Robótica Probabilística/Robótica Probabilística|Módulo 4: Robótica Probabilística]] (la base teórica $p(z \mid x, m)$ y los tres modelos canónicos)
- ➡️ [[5. Filtros Bayesianos/Filtros Bayesianos|Módulo 5: Filtros Bayesianos]] (los filtros consumen los modelos de sensor)
- ➡️ [[6. Mapeo/Mapeo|Módulo 6: Mapeo]] (likelihood field se computa desde mapa de ocupación)

## Páginas en este módulo
- [[Sensores Internos]]
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]]
