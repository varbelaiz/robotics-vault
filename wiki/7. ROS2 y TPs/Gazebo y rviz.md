---
modulo: 7. ROS2 y TPs
estado: en-progreso
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# Gazebo y rviz

> Las dos herramientas visuales del stack: **Gazebo** simula físicamente al robot y su entorno; **RVIZ** muestra qué "ve" el robot (poses, lidar, mapas, paths). Son complementarias, no alternativas.

> [!warning] Página parcial — se completa con Tutorial 3 + Tutorial 4 (Fase 2)
> Esta página tiene la introducción del Tutorial 1. El detalle operativo (cómo levantar mundos, configurar displays en RVIZ, escribir launch files que combinen ambos) viene del Tutorial 3 (Interactuando con Gazebo) y el Tutorial 4 (rviz + launch).

## Gazebo — simular el robot y el entorno

![[Gazebo - simulacion entorno.png]]
*Gazebo simulando un Turtlebot3 dentro de un mundo con obstáculos cilíndricos; el lidar se ve como rayos azules, slide 27.*

Gazebo es un **simulador físico** open source mantenido por Open Robotics. En esta cursada se usa con el modelo Turtlebot3 (`burger`) en mundos custom o en `empty_world`/`turtlebot3_world`.

> [!info] Limitación importante
> Gazebo opera con la **GPU**. Necesita al menos 1 GB de VRAM. La interacción con la GPU varía según arquitectura del CPU (ARM, AMD, Intel) — ARM (Apple Silicon, M1+) es la **menos compatible**.

## RVIZ — visualizar lo que el robot ve

![[RVIZ - visualizacion 3D.png]]
*RVIZ mostrando datos del robot: lidar scan, mapa, ejes de coordenadas, path planificado, slide 28.*

RVIZ no simula nada — es un **visor 3D** de datos publicados en topics ROS. Se configuran "displays" para cada tipo de dato:

| Dato | Display típico |
|---|---|
| LIDAR | `LaserScan` |
| Mapa | `Map` u `OccupancyGrid` |
| Pose del robot | `RobotModel` + `TF` |
| Path planificado | `Path` |
| Costmap | `Map` con colormap |

RVIZ requiere [[ROS2 - TF2]] correctamente publicado para renderizar las posiciones relativas entre frames.

## Conexiones
- [[ROS2 - Conceptos Base]] — framework.
- [[ROS2 - TF2]] — RVIZ depende de TF para renderizar.
- [[ROS2 - Launch Files]] — un launch típico levanta Gazebo + RVIZ + nodos del robot.
- [[Tutorial 3 - Interactuando con Gazebo]] — práctica detallada.
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — uso combinado.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf`
  - slide 27 → Gazebo
  - slide 28 → RVIZ
- `Raw/Diapositivas/Tutoriales/Tutorial 3_ Interactuando con Gazebo.pdf` *(pendiente Fase 2)*
- `Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf` *(pendiente Fase 2)*
