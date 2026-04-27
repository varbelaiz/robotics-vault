---
modulo: 7. ROS2 y TPs
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# ROS2 - Mensajes

> Tipos de datos **estructurados** que viajan por los [[ROS2 - Topics]]. Cada topic está fijado a un único tipo de mensaje — los publishers y subscribers tienen que coincidir o no se conectan.

## Prerequisitos
- [[ROS2 - Conceptos Base]] — los mensajes son uno de los 3 conceptos nucleares.
- [[ROS2 - Topics]] — los topics son tipados.

## 1. Idea

Un mensaje es un **struct** definido en un archivo `.msg` (o `.srv`, `.action`). ROS2 trae un set base de paquetes con tipos comunes:

| Paquete | Mensaje típico | Uso |
|---|---|---|
| `std_msgs` | `String`, `Int32`, `Float64`, `Bool` | tipos primitivos envueltos |
| `geometry_msgs` | `Pose`, `Twist`, `Quaternion`, `Vector3` | geometría 3D |
| `sensor_msgs` | `LaserScan`, `Image`, `Imu` | datos crudos de sensores |
| `nav_msgs` | `Odometry`, `OccupancyGrid`, `Path` | navegación / mapas |
| `tf2_msgs` | `TFMessage` | transformaciones de [[ROS2 - TF2]] |

## 2. Uso desde Python

Importar el tipo del paquete correspondiente y usarlo como un objeto Python:

```python
from std_msgs.msg import String

msg = String()
msg.data = f'hello world {self.get_clock().now().to_msg().sec}'
```

El `import` declara qué tipo se va a usar; el `String()` instancia un mensaje vacío que se llena por atributos.

> [!info] Mismo tipo en publisher y subscriber
> Si un [[ROS2 - Publisher]] manda un `std_msgs/String` y el [[ROS2 - Subscriber]] declara que escucha `std_msgs/Int32`, **no se conectan** — el descubrimiento DDS los emparejará sólo cuando coincidan.

## 3. Crear mensajes propios

Cuando los tipos del ecosistema no alcanzan, se define un paquete `<mi_paquete>_msgs` con archivos `.msg` propios. Esto se cubre en [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]].

## Conexiones
- [[ROS2 - Conceptos Base]] — uno de los 3 conceptos nucleares.
- [[ROS2 - Topics]] — el topic está fijado a un tipo de mensaje.
- [[ROS2 - Publisher]] — declara el tipo al crear el publisher.
- [[ROS2 - Subscriber]] — declara el tipo al crear la subscription.
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — definición de mensajes custom.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf`
  - slide 13 → uso de `std_msgs/String` en el Talker
  - slide 34 → uso del mismo tipo en el Listener
