---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 3_ Interactuando con Gazebo.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# Tutorial 3 — Interactuando con Gazebo

> Lanzar la simulación, entender el grafo de nodos/topics que aparece, introducir [[ROS2 - TF2|TF2]] y los frames, mover el robot (manual + código), y grabar datos.

## Objetivo

Que cada estudiante, al terminar, pueda:

1. Lanzar `turtlebot3_custom_simulation` y entender qué nodos y topics aparecen.
2. Saber qué es `/tf` y cómo inspeccionar el árbol de frames.
3. Usar `ros2 topic list/echo/pub` para inspeccionar y manipular el grafo.
4. Mover el robot con `teleop_keyboard` y con un nodo Python propio.
5. Grabar el recorrido del robot (`/odom`) y graficarlo offline.

## Conceptos que se introducen
- [[ROS2 - TF2]] — frames, transformaciones dinámicas vs. estáticas, `view_frames`.
- [[Gazebo y rviz]] — Gazebo en detalle, `rqt_graph`.
- [[ROS2 - Mensajes]] — primer encuentro con `nav_msgs/Odometry`, `sensor_msgs/JointState`, `geometry_msgs/Twist`.

## Estructura del tutorial

| Slides | Sección |
|---|---|
| 2–6 | Hablemos de la simulación: rqt_graph + nodos del Turtlebot |
| 7–14 | ¿Qué es la TF? + view_frames |
| 15–22 | Comandos `ros2 topic list/echo/pub` |
| 23–26 | Mover el robot con teleop_keyboard |
| 27–31 | Tarea: enviar comandos aleatorios desde código |
| 32–33 | Toma/visualización de datos: rosbag y código Recorder |

## 1. Hablemos de la simulación

```bash
ros2 launch turtlebot3_custom_simulation custom_simulation.launch.py
```

Esto levanta varios nodos. El comando `rqt_graph` muestra el grafo completo:

![[TF - rqt graph turtlebot.png]]
*Grafo del custom_simulation: nodos publicando datos del robot (`/turtlebot3_lasercan` → `/scan`, `/turtlebot3_imu` → `/imu`, `/turtlebot3_joint_state` → `/joint_states` → `/robot_state_publisher` → `/tf`), publishers de TF estática y dinámica, gazebo, slide 3.*

Categorización por color (mismo grafo, slide 18):

![[Topics - rqt graph colorized.png]]
*Verde: nodos. Violeta: topics regulares. Naranja: el topic `/tf` (transformaciones), slide 18.*

Resumen de qué nodos aparecen y qué hace cada uno:

| Nodo | Rol |
|---|---|
| `/gazebo` | El simulador físico |
| `/turtlebot3_diff_drive` | Plugin de Gazebo, traduce `/cmd_vel` → movimiento + publica `/odom` y `/tf` (odom→base_footprint) |
| `/turtlebot3_laserscan` | Plugin del lidar simulado, publica `/scan` |
| `/turtlebot3_imu` | Plugin del IMU, publica `/imu` |
| `/turtlebot3_joint_state` | Publica `/joint_states` (posiciones de las ruedas) |
| `/robot_state_publisher` | Convierte `/joint_states` + URDF → transformaciones de cada joint en `/tf` |
| `/static_tf_pub_map_to_odom` | Publica la transformación estática `map → odom` |

## 2. ¿Qué es la TF?

Detalle completo en [[ROS2 - TF2]]. Resumen:

- Cada parte del sistema (mapa global, robot, lidar, etc.) tiene su propio **frame** (terna de coordenadas).
- TF2 mantiene un **árbol** con todas las transformaciones entre frames.
- Hay transformaciones **dinámicas** (cambian en el tiempo, ej. `odom → base_link`) y **estáticas** (fijas, ej. `base_link → base_scan`).

Para inspeccionar el árbol completo del Turtlebot3:

![[TF - view frames arbol.png]]
*`ros2 run tf2_tools view_frames` genera frames.pdf con el árbol: `odom → base_footprint → base_link → {camera_link, caster_*, imu_link, base_scan, wheel_*}`, slide 14.*

```bash
ros2 run tf2_tools view_frames
```

## 3. Inspeccionar topics

```bash
ros2 topic list
```

![[Topics - lista turtlebot.png]]
*Lista de topics publicados por la simulación: `/calc_odom, /clock, /cmd_vel, /imu, /joint_states, /odom, /parameter_events, /performance_metrics, /robot_description, /rosout, /scan, /tf, /tf_static`, slide 17.*

Algunos son **debug** (de bajo interés): `/clock`, `/parameter_events`, `/performance_metrics`, `/rosout`. El resto es lo que importa para el control.

Para ver mensajes en vivo:

```bash
ros2 topic echo /joint_states
```

![[Topics - echo joint states.png]]
*Salida: `header` (timestamp) + `name` (wheel_left_joint, wheel_right_joint) + `position` + `velocity`, slide 21.*

## 4. Mover el robot

### Publicar `/cmd_vel` a mano

```bash
ros2 topic pub /cmd_vel geometry_msgs/msg/Twist \
  "{ linear: { x: 0.2, y: 0.0, z: 0.0 }, angular: { x: 0.0, y: 0.0, z: 0.5 } }"
```

![[Topics - pub cmd vel lidar.png]]
*Comando `pub /cmd_vel` con velocidad lineal + angular hace al robot girar; el lidar dibuja un círculo azul gigante por el barrido continuo, slide 22.*

### Teleop con teclado

```bash
ros2 run turtlebot3_teleop teleop_keyboard
```

Controles: `w/x` lineal, `a/d` angular, `s/space` stop. Arranca un nodo `/teleop_keyboard` que publica al mismo topic `/cmd_vel`:

![[Tutorial 3 - rqt graph teleop.png]]
*El grafo ahora tiene `/teleop_keyboard` (en rojo) publicando a `/cmd_vel`, que llega a `/turtlebot3_diff_drive`, slide 26.*

### Comandos aleatorios desde código (tarea)

> Quiero que el robot se mueva a 0.2 m/s en x, con velocidad angular en z aleatoria entre $-\pi/2$ y $\pi/2$, cambiando cada 4 segundos.

![[Tutorial 3 - Commander code.png]]
*Nodo `Commander` con timer a 0.05 s; cada 80 ticks (= 4 s) randomiza `self.w`. Publica `Twist` a `/cmd_vel`, slide 30.*

```python
import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist
import random
import numpy as np

class Commander(Node):
    def __init__(self):
        super().__init__('commander')
        self.publisher = self.create_publisher(Twist, '/cmd_vel', 10)
        self.timer = self.create_timer(0.05, self.timer_callback)
        self.counter = 0
        self.target = int(4 / 0.05)
        self.w = 0.0

    def timer_callback(self):
        if self.counter == self.target:
            self.counter = 0
            self.w = random.uniform(-1, 1) * np.pi / 2
            self.get_logger().info(f"Setting angular velocity to {self.w}")
        else:
            self.counter += 1
        msg = Twist()
        msg.linear.x = 0.2
        msg.angular.z = self.w
        self.publisher.publish(msg)
```

## 5. Toma y visualización de datos

> Bien, ahora quiero que al finalizar la ejecución me hagan un gráfico del camino que hizo el robot.

Opciones:

1. **rosbag** — graba todos los topics a un archivo, replay offline. (Para usos más avanzados.)
2. **Subscriber + atexit** — más simple para este caso: un nodo se suscribe a `/odom`, acumula `(x, y)` en memoria, y al cerrar guarda CSV.

![[Tutorial 3 - Recorder code.png]]
*Nodo `Recorder` que se suscribe a `/odom`, acumula coordenadas, y registra `save()` con `atexit` para volcar a CSV cuando se cierra el programa, slide 33.*

```python
import rclpy
from rclpy.node import Node
import os, atexit
from pathlib import Path
from nav_msgs.msg import Odometry

class Recorder(Node):
    def __init__(self):
        super().__init__('recorder')
        self.data_sub = self.create_subscription(
            Odometry, '/odom', self.odom_callback, 10)
        self.coordinates_x = []
        self.coordinates_y = []
        self.dataLoc = r"turtlebot3_ws/src/commander"
        self.datafile = os.path.join(str(Path.home()), self.dataLoc, 'coord.csv')

    def odom_callback(self, msg):
        self.coordinates_x.append(msg.pose.pose.position.x)
        self.coordinates_y.append(msg.pose.pose.position.y)

    def save(self):
        with open(self.datafile, 'w') as file:
            file.write('x,y\n')
            for x, y in zip(self.coordinates_x, self.coordinates_y):
                file.write(f'{x},{y}\n')

def main(args=None):
    rclpy.init(args=args)
    node = Recorder()
    atexit.register(node.save)
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()
```

Después se grafica con matplotlib offline.

## Conexiones
- [[Tutorial 2 - Speaker and Listener]] — tutorial anterior (pub/sub básico).
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — siguiente: RVIZ, mensajes complejos, launch files, TP1/TP2.
- [[ROS2 - TF2]] — concepto que se introduce acá.
- [[Gazebo y rviz]] — Gazebo en detalle.
- [[Modelo de Movimiento (Velocidad)]] — el `Twist` es exactamente el control $(v, \omega)$ del modelo de velocidad.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 3_ Interactuando con Gazebo.pdf`
  - slides 2–6 → 1. Hablemos de la simulación
  - slides 7–14 → 2. ¿Qué es la TF?
  - slides 15–22 → 3. Inspeccionar topics
  - slides 23–26 → 4.1 Teleop
  - slides 27–31 → 4.2 Comandos aleatorios desde código
  - slides 32–33 → 5. Toma y visualización
