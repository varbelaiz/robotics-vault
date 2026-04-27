---
modulo: 7. ROS2 y TPs
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
  - Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf
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

## 3. Composición jerárquica

Los mensajes complejos están **construidos a partir de otros mensajes**. Esto se ve clarísimo con `nav_msgs/Odometry`, que es el corazón del [[TP1 - Transformaciones, Locomoción y Sensado|TP1]]:

![[Mensajes - nav msgs Odometry.png]]
*`nav_msgs/Odometry`: header + child_frame_id + pose (con covarianza) + twist (con covarianza), slide 10 de T4.*

Su estructura:

```
nav_msgs/Odometry
├── std_msgs/Header header              # timestamp + frame_id
├── string child_frame_id                # frame del twist
├── geometry_msgs/PoseWithCovariance pose
│   ├── geometry_msgs/Pose pose
│   │   ├── geometry_msgs/Point position    # x, y, z (m)
│   │   └── geometry_msgs/Quaternion orientation  # x, y, z, w
│   └── float64[36] covariance
└── geometry_msgs/TwistWithCovariance twist
    ├── geometry_msgs/Twist twist
    │   ├── geometry_msgs/Vector3 linear   # x, y, z (m/s)
    │   └── geometry_msgs/Vector3 angular  # x, y, z (rad/s)
    └── float64[36] covariance
```

![[Mensajes - Pose Point Quaternion.png]]
*Pose se descompone en Point (x,y,z) + Quaternion (x,y,z,w). El quaternion default es (0,0,0,1) = sin rotación, slide 15 de T4.*

> [!info] La rotación viene como cuaternión
> `geometry_msgs/Quaternion` tiene los 4 floats. Para razonar como humano (yaw escalar) hay que convertir — ver [[Cuaterniones]].

### Path = array de PoseStamped

![[Mensajes - Path PoseStamped.png]]
*`nav_msgs/Path` = header + array de `PoseStamped`. Cada PoseStamped es header + Pose, slide 17 de T4.*

```
nav_msgs/Path
├── std_msgs/Header header
└── geometry_msgs/PoseStamped[] poses
    ├── std_msgs/Header header
    └── geometry_msgs/Pose pose
```

Esta composición permite que un `Path` se renderice en RVIZ como una secuencia de poses navegables.

## 4. Crear mensajes propios

Cuando los tipos del ecosistema no alcanzan, se define un paquete `<mi_paquete>_msgs` con archivos `.msg` propios. La declaración usa la misma sintaxis de composición ("nombre tipo" por línea, con primitivos `int32`, `float64`, `string`, `bool`, etc., o referencias a otros paquetes). Esto se profundiza en tutoriales más avanzados; en TPs del curso alcanza con los tipos del ecosistema base.

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
