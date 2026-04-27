> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# Módulo 7 — ROS2

> Framework ROS2 + tutoriales de la cursada. Cubre los conceptos de infraestructura del middleware (nodos, topics, mensajes, TF2, launch files, Gazebo y RVIZ) y los 8 tutoriales que aterrizan los módulos teóricos en código.

## Por qué este módulo

Todo el curso culmina en código real corriendo sobre un Turtlebot3 simulado. M7 cubre dos capas:

1. **Conceptos de infraestructura ROS2** — el middleware en sí: cómo se comunican los nodos, qué tipos de mensajes hay, cómo se lanzan, etc.
2. **Tutoriales** — secuencia diseñada T1→T8 que va de la instalación al filtro de Kalman extendido, aplicando los modelos teóricos de M1–M6 en ROS2.

Los **trabajos prácticos** (TP1–TP3) viven en su propio módulo, [[TPs|Módulo 8]].

## Recorrido sugerido

### Conceptos de infraestructura

1. [[ROS2 - Conceptos Base]] — middleware, arquitectura, stack del curso.
2. [[ROS2 - Nodos]] — el patrón timer + callback.
3. [[ROS2 - Topics]] — modelo pub/sub asíncrono.
4. [[ROS2 - Mensajes]] — tipos estructurados, composición jerárquica.
5. [[ROS2 - Publisher]] — lado emisor.
6. [[ROS2 - Subscriber]] — lado receptor reactivo.
7. [[Gazebo y rviz]] — simulación + visualización (Gazebo, RVIZ con displays/views/frames).
8. [[ROS2 - TF2]] — frames, transformaciones dinámicas vs. estáticas, árbol completo.
9. [[ROS2 - Launch Files]] — orquestar múltiples nodos con `generate_launch_description()`.

### Tutoriales

1. [[Tutorial 1 - Bienvenida e Instalación de ROS2]] — entorno + instalación.
2. [[Tutorial 2 - Speaker and Listener]] — pub/sub básico + anticipo de TP1 ([[Cuaterniones]]).
3. [[Tutorial 3 - Interactuando con Gazebo]] — Gazebo + [[ROS2 - TF2|TF2]] + comandos topic + Recorder.
4. [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — RVIZ, composición de mensajes, launch files, preview de [[TP1 - Transformaciones, Locomoción y Sensado|TP1]] y [[TP2 - Modelos Probabilísticos y Filtros Discretos|TP2]].
5. [[Tutorial 5 - Bayes]] — ejercicio del sensor defectuoso ([[Regla de Bayes]] recursivo).
6. [[Tutorial 6 - Movimiento, Sensores e Histogramas]] — aterriza [[Odometría y Modelo de Movimiento (Odometría)]] + [[Modelo de Sensor Basado en Haz]] + [[Modelo de Campo de Verosimilitud]] + [[Filtros Discretos]].
7. [[Tutorial 7 - Filtro de Partículas]] — aterriza [[MCL - Filtro de Partículas]] con SUS resampling.
8. [[Tutorial 8 - KF, EKF, UKF]] — aterriza [[Filtro de Kalman]] / [[EKF]] / [[UKF]] + integración en ROS2 para [[TP3 - Filtros de Partículas|TP3]].

## Conecta con

- ⬅️ Todos los módulos teóricos (M1–M6) — los tutoriales aplican sus modelos en código.
- ➡️ [[TPs|Módulo 8: TPs]] — los entregables del curso integran ROS2 + teoría.

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
