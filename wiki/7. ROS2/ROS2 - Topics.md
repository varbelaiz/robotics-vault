---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# ROS2 - Topics

> Canal de comunicación **asíncrono pub/sub** entre [[ROS2 - Nodos]]: cualquier publisher escribe en el topic; cualquier subscriber lo recibe. Sin handshake, sin garantías de entrega — el "fire-and-forget" del grafo.

## Prerequisitos
- [[ROS2 - Conceptos Base]] — el topic es uno de los 3 componentes nucleares.
- [[ROS2 - Nodos]] — los topics conectan nodos entre sí.

## 1. Modelo pub/sub

![[ROS - arquitectura nodes topics.png]]
*Cualquier nodo puede publicar a un topic y cualquier nodo puede suscribirse. Los topics admiten 1-a-N, N-a-1 y N-a-N, slide 20 de T1.*

Características clave:

- **Asíncrono**: el publisher no espera ack del subscriber.
- **Tipado**: todos los mensajes en un topic deben ser del **mismo tipo** ([[ROS2 - Mensajes]]).
- **Descubrimiento dinámico**: publishers y subscribers se descubren en tiempo de ejecución vía DDS.
- **Multiplicidad libre**: 0..N publishers + 0..N subscribers por topic.

### Flujo entre nodos

![[Talker Listener - flujo completo.png]]
*Talker publica al topic `chatter`; listener lo recibe vía un Subscriber + callback, slide 30 de T2.*

El **listener no usa wall_timer** — reacciona al evento "llegó mensaje al topic", invocando el callback que registró. Esto es lo opuesto al [[ROS2 - Publisher|publisher]], que sí usa timer para emitir periódicamente.

## 2. Topics típicos en este curso

| Topic | Tipo | Origen |
|---|---|---|
| `/odom` | `nav_msgs/Odometry` | Driver de odometría del robot |
| `/cmd_vel` | `geometry_msgs/Twist` | Controlador de movimiento |
| `/scan` | `sensor_msgs/LaserScan` | Sensor LIDAR |
| `/tf`, `/tf_static` | `tf2_msgs/TFMessage` | [[ROS2 - TF2]] broadcaster |
| `/chatter` | `std_msgs/String` | Ejemplo del Tutorial 2 |

## 3. Inspección desde la terminal

```bash
# Listar todos los topics activos
ros2 topic list

# Detalle de un topic (tipo + publishers + subscribers)
ros2 topic info /scan

# Ver mensajes en tiempo real
ros2 topic echo /scan

# Frecuencia de publicación
ros2 topic hz /odom
```

## Conexiones
- [[ROS2 - Conceptos Base]] — uno de los 3 componentes nucleares.
- [[ROS2 - Mensajes]] — qué viaja por el topic (tipado).
- [[ROS2 - Publisher]] — lado emisor.
- [[ROS2 - Subscriber]] — lado receptor.
- [[ROS2 - Nodos]] — ambos extremos.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf`
  - slide 20 → 1. Modelo pub/sub
- `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf`
  - slides 28–32 → 1. Flujo entre nodos
