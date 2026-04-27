> [[Sensores y Modelos|← Sensores y Modelos]] | [[Robotica|← Inicio]]

# Módulo 3 — Sensores y Modelos

> Qué sensores usa un robot móvil para percibir su estado y su entorno, y cómo se modela la incertidumbre de cada medición — el "modelo de observación" que consumen los filtros de Bayes.

## Por qué este módulo
Toda localización y mapeo dependen de medir el entorno. Pero los sensores son ruidosos, sesgados, y a veces directamente fallan (cristal, espejos, crosstalk). Este módulo separa **qué hardware existe** (encoders, IMU, GPS, sonar, lidar, cámaras) de **cómo modelar probabilísticamente sus mediciones** ($P(z \mid x, m)$ por beam-based, likelihood field o landmarks).

## Recorrido sugerido
1. [[Sensores Internos]] — clasificación, encoders, táctiles, IMU, tabla resumen.
2. [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — los exteroceptivos a distancia.
3. [[Modelo de Sensor Basado en Haz]] — modelo canónico para sonar/lidar (mezcla de hit/unexp/max/rand).
4. [[Modelo de Campo de Verosimilitud]] — alternativa eficiente y suave.
5. [[Modelo de Detección de Landmarks]] — modelo cuando se observan marcadores conocidos.

## Conecta con
- ⬅️ [[1. Fundamentos/Fundamentos|Módulo 1: Fundamentos]] (matrices, atan2, gaussianas)
- ⬅️ [[2. Locomoción/Locomoción|Módulo 2: Locomoción]] (los encoders alimentan odometría)
- ➡️ [[4. Robótica Probabilística/Robótica Probabilística|Módulo 4: Probabilidad]] (la base teórica de $p(z \mid x, m)$)
- ➡️ [[5. Filtros Bayesianos/Filtros Bayesianos|Módulo 5: Filtros Bayesianos]] (los filtros consumen los modelos de sensor)
- ➡️ [[6. Mapeo/Mapeo|Módulo 6: Mapeo]] (likelihood field se computa desde mapa de ocupación)

## Páginas en este módulo
- [[Sensores Internos]]
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]]
- [[Modelo de Sensor Basado en Haz]]
- [[Modelo de Campo de Verosimilitud]]
- [[Modelo de Detección de Landmarks]]
