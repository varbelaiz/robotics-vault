---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 3_ Interactuando con Gazebo.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# ROS2 - TF2

> Sistema de **transformaciones de coordenadas** en ROS2: gestiona la jerarquía de frames (mapa, robot, base_link, sensor, etc.) y permite consultar la transformación entre cualquier par de ellos en cualquier instante de tiempo. Es la pieza que hace que un dato del lidar (`/scan` en frame `base_scan`) se pueda dibujar en el mapa global.

## Prerequisitos
- [[Transformaciones Homogéneas]] — base teórica.
- [[Cuaterniones]] — TF2 codifica la rotación con cuaterniones.
- [[ROS2 - Topics]] — TF2 publica en `/tf` y `/tf_static`.

## 1. Frames y transformaciones

Un **frame** es un sistema de referencia con nombre. Cada componente del sistema vive en su propio frame:

![[TF - frames mapa robot sensor.png]]
*Frame "Mapa" (terna global) ↔ frame "Robot" (en coordenadas (2, 3) y rotado 90° en z) ↔ frame "Sensor" (en z+0.1 sobre el robot, rotado -90° en z), slide 10 de T3.*

La transformación entre frames es lo que permite expresar un mismo punto del mundo en cualquiera de ellos. Esto es exactamente la composición de [[Transformaciones Homogéneas]] del Módulo 1, llevada al runtime: TF2 mantiene **el grafo de transformaciones** y permite consultar sobre él.

## 2. Transformaciones dinámicas vs. estáticas

Hay dos clases de transformaciones según evolucionen o no en el tiempo:

![[TF - dinamicas vs estaticas.png]]
*Las dos cajas: Transformaciones Dinámicas (rojo, dotted) vs Transformaciones Estáticas (azul), slide 11 de T3.*

![[TF - dinamicas vs estaticas detalle.png]]
*Detalle: dinámicas se modifican en vivo; estáticas se definen al inicio y no cambian salvo reemplazo, slide 12 de T3.*

| | Dinámicas | Estáticas |
|---|---|---|
| **Inicio** | Se definen con un valor inicial | Se definen una vez |
| **Modificación** | Pueden cambiar en cada paso (vivo) | No se modifican (excepto re-publicar) |
| **Topic** | `/tf` | `/tf_static` |
| **Ejemplo** | `odom → base_footprint` (el robot se mueve) | `base_link → base_scan` (el lidar está atornillado) |

> [!info] Por qué importa la separación
> Las estáticas se publican con QoS `transient_local` — quien se suscriba después igual recibe el último valor. Las dinámicas se republican alta-frecuencia (10–100 Hz) y descartan lo viejo.

## 3. El árbol de transformaciones

TF2 organiza los frames como un **árbol** con un único frame raíz. El comando `ros2 run tf2_tools view_frames` genera un PDF con el árbol completo:

![[TF - view frames arbol.png]]
*Árbol del Turtlebot3 en simulación: `odom → base_footprint → base_link → {camera_link, caster_back_*, imu_link, base_scan, wheel_*}`, con cámara expandiéndose a sus sub-frames. Cada arista lista el broadcaster, average rate y buffer length, slide 14 de T3.*

```bash
# Generar el árbol completo del sistema actual
ros2 run tf2_tools view_frames
# (esto crea frames.pdf en el directorio actual)

# Consultar una transformación específica desde la terminal
ros2 run tf2_ros tf2_echo <source_frame> <target_frame>
```

> [!warning] Una sola raíz
> Si dos broadcasters publican la misma transformación con padres distintos (ej. uno publica `world → odom` y otro `map → odom`), TF2 emite warning de "TF_OLD_DATA" o conflictos. Cada frame **debe tener un único padre**.

## 4. Topics involucrados

```bash
$ ros2 topic list
...
/tf
/tf_static
```

- **`/tf`** — `tf2_msgs/TFMessage`, dinámicas.
- **`/tf_static`** — `tf2_msgs/TFMessage`, estáticas (latched).

Los nodos suelen no suscribirse directamente — usan la API `tf2_ros.Buffer + TransformListener`, que abstrae el ciclo de buffereo + interpolación temporal.

## 5. En el grafo de simulación

Cuando se lanza `turtlebot3_custom_simulation`, varios nodos publican a `/tf`:

- `/robot_state_publisher` — convierte `/joint_states` + el URDF en transformaciones de cada joint.
- `/static_tf_pub_map_to_odom` — publica la transformación estática `map → odom`.
- `/turtlebot3_diff_drive` — publica `odom → base_footprint` con la pose del modelo de movimiento diferencial.

Ver [[Tutorial 3 - Interactuando con Gazebo]] para el grafo completo y los topics que coexisten.

## 6. API en Python

```python
import rclpy
from rclpy.node import Node
from tf2_ros import Buffer, TransformListener

class MiNode(Node):
    def __init__(self):
        super().__init__('mi_nodo')
        self.tf_buffer = Buffer()
        self.tf_listener = TransformListener(self.tf_buffer, self)

    def consultar(self):
        try:
            tf = self.tf_buffer.lookup_transform('map', 'base_link', rclpy.time.Time())
            x = tf.transform.translation.x
            y = tf.transform.translation.y
            qz = tf.transform.rotation.z  # cuaternión
        except Exception as e:
            self.get_logger().warn(f'No TF: {e}')
```

## Conexiones
- [[Transformaciones Homogéneas]] — la matemática que TF2 implementa.
- [[Cuaterniones]] — formato de la rotación en `geometry_msgs/Transform`.
- [[ROS2 - Topics]] — `/tf` y `/tf_static`.
- [[Gazebo y rviz]] — RVIZ depende de TF para renderizar.
- [[Tutorial 3 - Interactuando con Gazebo]] — práctica.
- [[TP1 - Transformaciones, Locomoción y Sensado]] — primera aplicación intensiva.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 3_ Interactuando con Gazebo.pdf`
  - slides 7–10 → 1. Frames y transformaciones
  - slides 11–12 → 2. Dinámicas vs. estáticas
  - slide 14 → 3. El árbol de transformaciones
  - slide 17 → 4. Topics involucrados
  - slides 3–6 → 5. En el grafo de simulación
