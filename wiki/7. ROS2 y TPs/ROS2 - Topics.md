---
modulo: 7. ROS2 y TPs
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# ROS2 - Topics

> Canal de comunicación asíncrono basado en pub/sub entre [[ROS2 - Nodos]].

## Modelo de comunicación  *(Tutorial 2, págs. 1–4)*

Un **topic** es un canal nominal por el cual fluyen mensajes de uno o más publishers hacia uno o más subscribers. El modelo es **fire-and-forget** (sin garantía de entrega):

```
Publisher ──┐
            ├──(topic: "chatter")──> Subscriber
Publisher ──┘
```

Características clave:
- **Asíncrono**: publisher no espera confirmación del subscriber
- **Tipado**: todos los mensajes en un topic deben ser del mismo tipo
- **Descubrimiento dinámico**: publishers y subscribers se descubren en tiempo de ejecución (DDS)

### Topics comunes en robótica  *(Tutorial 3–4, págs. 5–12)*

| Topic | Tipo | Origen |
|---|---|---|
| `/odom` | `nav_msgs/Odometry` | Driver de odometría |
| `/cmd_vel` | `geometry_msgs/Twist` | Controlador de movimiento |
| `/scan` | `sensor_msgs/LaserScan` | Sensor LIDAR |
| `/tf` | `tf2_msgs/TFMessage` | Broadcaster de transformaciones |

### Inspección  *(Tutorial 1, págs. 15–18)*

```bash
# Listar todos los topics activos
ros2 topic list

# Ver detalle de un topic
ros2 topic info /scan

# Escuchar mensajes en tiempo real
ros2 topic echo /scan
```

## Conexiones
- [[ROS2 - Conceptos Base]] — parte del framework
- [[ROS2 - Mensajes]] — lo que viaja por los topics
- [[ROS2 - Publisher]] — lado emisor
- [[ROS2 - Subscriber]] — lado receptor

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf`
