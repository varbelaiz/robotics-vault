---
modulo: 7. ROS2 y TPs
estado: esqueleto
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# ROS2 - Launch Files

> Archivos Python (`.launch.py`) que orquestan el arranque de **múltiples [[ROS2 - Nodos|nodos]] con configuración**: parámetros, remapeos de topics, namespaces, simulador + visualizador todo de una.

> [!warning] Pendiente — se completa con Tutorial 4 (Fase 2)
> Esta página es un esqueleto. El contenido grounded de launch files viene del Tutorial 4 (msgs, rviz, launch y TP1_TP2), que se ingiere en la próxima fase.

## Conceptos clave (preview)

- **`generate_launch_description()`** — función que devuelve la lista de acciones de arranque.
- **`Node(...)`** — declara un nodo a lanzar (paquete + ejecutable + parámetros + remapeos).
- **`IncludeLaunchDescription(...)`** — incluir otro launch file (composición).
- **Argumentos** y **sustituciones** para parametrizar el launch.
- Comando: `ros2 launch <pkg> <file>.launch.py`.

## Conexiones
- [[ROS2 - Conceptos Base]] — framework.
- [[ROS2 - Nodos]] — lo que se lanza.
- [[Gazebo y rviz]] — un launch típico levanta simulador + visualizador + nodos custom.
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — práctica.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf` *(pendiente Fase 2)*
