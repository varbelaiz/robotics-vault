> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# Módulo 7 — ROS2 y TPs

> Implementación práctica: framework ROS2, simulación con Gazebo/RVIZ, y trabajos prácticos que integran todo el curso.

## Por qué este módulo

Todo el curso culmina en código real. Los tutoriales introducen las herramientas ROS2 paso a paso, y los TPs obligan a integrar conceptos de módulos anteriores (transformaciones, filtros, sensores) en un sistema funcional.

## Estado actual

**Fases 1–5 ingestadas (2026-04-27)**: Tutoriales 1–8 + 9 conceptos de infra core con contenido grounded. Los 8 tutoriales del módulo están completos. Sólo quedan los TPs (Fase 6).

| Fase | Tutoriales | Estado |
|---|---|---|
| 1 | T1 + T2 | ✅ Completa |
| 2 | T3 + T4 | ✅ Completa |
| 3 | T5 | ✅ Completa |
| 4 | T6 | ✅ Completa |
| 5 | T7 + T8 | ✅ Completa |
| 6 | TP1 + TP2 + TP3 | Pendiente |

## Recorrido sugerido

### ROS2 — Conceptos (Fase 1)

1. [[ROS2 - Conceptos Base]] — middleware, arquitectura, stack del curso.
2. [[ROS2 - Nodos]] — el patrón timer + callback.
3. [[ROS2 - Topics]] — modelo pub/sub asíncrono.
4. [[ROS2 - Mensajes]] — tipos estructurados.
5. [[ROS2 - Publisher]] — lado emisor.
6. [[ROS2 - Subscriber]] — lado receptor reactivo.
7. [[Gazebo y rviz]] — simulación + visualización (Gazebo, RVIZ con displays/views/frames).
8. [[ROS2 - TF2]] — frames, transformaciones dinámicas vs. estáticas, árbol completo.
9. [[ROS2 - Launch Files]] — orquestar múltiples nodos con `generate_launch_description()`.

### Tutoriales

1. [[Tutorial 1 - Bienvenida e Instalación de ROS2]] — entorno + instalación. ✅
2. [[Tutorial 2 - Speaker and Listener]] — pub/sub básico + anticipo TP1. ✅
3. [[Tutorial 3 - Interactuando con Gazebo]] — Gazebo, TF2, comandos topic, mover el robot, rosbag. ✅
4. [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — RVIZ, composición de mensajes, launch files, preview TP1/TP2. ✅
5. [[Tutorial 5 - Bayes]] — Ejercicio del sensor defectuoso (Bayes recursivo + código + plot), fusión de sensores, derivación del filtro. ✅
6. [[Tutorial 6 - Movimiento, Sensores e Histogramas]] — `sample_motion_model_odometry`, modelo de sensor basado en haz + Bresenham, likelihood field, filtro de histograma. ✅
7. [[Tutorial 7 - Filtro de Partículas]] — MCL paso a paso (predicción + actualización con landmarks + remuestreo SUS), preview features. ✅
8. [[Tutorial 8 - KF, EKF, UKF]] — log-likelihood trick para MCL, derivación KF predict/correct desde la dinámica, EKF con Jacobianos, UKF con sigma points, TP4 paralelo. ✅

### TPs

1. [[TP1 - Transformaciones, Locomoción y Sensado]] — transformaciones + datos reales *(esqueleto)*.
2. [[TP2 - Modelos Probabilísticos y Filtros Discretos]] — muestreo + filtro histograma *(esqueleto)*.
3. [[TP3 - Filtros de Partículas]] — MCL completo en ROS2 *(esqueleto)*.

## Conecta con

- ⬅️ Todos los módulos teóricos (M1–M6) — los TPs integran todo.
- T1 + T2 introducen [[Cuaterniones]] (M1) como anticipo de TP1.

## Páginas en este módulo

- [[ROS2 - Conceptos Base]]
- [[ROS2 - Nodos]]
- [[ROS2 - Topics]]
- [[ROS2 - Mensajes]]
- [[ROS2 - Publisher]]
- [[ROS2 - Subscriber]]
- [[ROS2 - TF2]]
- [[ROS2 - Launch Files]]
- [[Gazebo y rviz]]
- [[Tutorial 1 - Bienvenida e Instalación de ROS2]]
- [[Tutorial 2 - Speaker and Listener]]
- [[Tutorial 3 - Interactuando con Gazebo]]
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]]
- [[Tutorial 5 - Bayes]]
- [[Tutorial 6 - Movimiento, Sensores e Histogramas]]
- [[Tutorial 7 - Filtro de Partículas]]
- [[Tutorial 8 - KF, EKF, UKF]]
- [[TP1 - Transformaciones, Locomoción y Sensado]]
- [[TP2 - Modelos Probabilísticos y Filtros Discretos]]
- [[TP3 - Filtros de Partículas]]
