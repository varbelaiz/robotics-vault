---
modulo: 7. ROS2 y TPs
estado: esqueleto
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 3_ Interactuando con Gazebo.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# ROS2 - TF2

> Sistema de **transformaciones de coordenadas** en ROS2: gestiona la jerarquía de frames (base_link, odom, map, laser, etc.) y la consulta temporal entre cualquier par de frames.

> [!warning] Pendiente — se completa con Tutorial 3 (Fase 2)
> Esta página es un esqueleto. El contenido grounded de TF2 viene del Tutorial 3 (Interactuando con Gazebo), que se ingiere en la próxima fase.

## Conceptos clave (preview)

- **Frame**: sistema de referencia nominado.
- **Tree de transformaciones**: jerarquía de frames con relaciones padre→hijo.
- **Broadcaster** (`tf2_ros.TransformBroadcaster`) — publica transformaciones nuevas.
- **Listener** (`tf2_ros.Buffer + TransformListener`) — consulta una transformación entre dos frames en un timestamp dado.
- **Topics asociados**: `/tf` (dinámicas), `/tf_static` (estáticas).

## Conexiones
- [[Transformaciones Homogéneas]] — base teórica.
- [[Cuaterniones]] — TF2 usa cuaterniones para la parte rotacional.
- [[Gazebo y rviz]] — RVIZ requiere TF para renderizar correctamente.
- [[Tutorial 3 - Interactuando con Gazebo]] — práctica.
- [[TP1 - Transformaciones, Locomoción y Sensado]] — primera aplicación.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 3_ Interactuando con Gazebo.pdf` *(pendiente Fase 2)*
