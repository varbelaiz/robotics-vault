---
modulo: 7. ROS2 y TPs
estado: esqueleto
fuentes:
  - Raw/TPs/Enunciado TP3.pdf
ultima_actualizacion: 2026-04-26
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# TP3 - Filtros de Partículas

> Implementación completa de [[MCL - Filtro de Partículas]] en ROS2 con campo de verosimilitud.

## Conceptos necesarios
- [[MCL - Filtro de Partículas]] — localización Monte Carlo
- [[Modelo de Campo de Verosimilitud]] — sensor LIDAR sobre mapa
- [[Modelo de Movimiento (Odometría)]] — movimiento de partículas
- [[Mapas de Ocupación]] — mapa base para likelihood field

## Pasos del enunciado
1. Proceso de lanzamiento de nodos (simulación + ROS2)
2. Campo de verosimilitud: procesar mapa y generar grilla de probabilidad
3. Movimiento de partículas: actualizar poses con odometría + ruido

## Conexiones
- [[Tutorial 7 - Filtro de Partículas]] — teoría aplicada
- [[Tutorial 8 - KF, EKF, UKF]] — alternativa con Kalman

## Fuentes
- `Raw/TPs/Enunciado TP3.pdf`
