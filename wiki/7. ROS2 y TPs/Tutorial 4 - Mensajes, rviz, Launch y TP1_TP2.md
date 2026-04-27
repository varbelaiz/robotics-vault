---
modulo: 7. ROS2 y TPs
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# Tutorial 4 — Mensajes, rviz, Launch y TP1/TP2

> Aterrizar en RVIZ como visualizador, entender la **composición jerárquica de mensajes** complejos (Odometry, Path), aprender a escribir [[ROS2 - Launch Files|launch files]], y previsualizar los entregables de TP1 y TP2.

## Objetivo

Que cada estudiante, al terminar, pueda:

1. Configurar RVIZ con displays apropiados para los datos del robot.
2. Leer la definición de un mensaje complejo (`nav_msgs/Odometry`, `nav_msgs/Path`) y construirlo desde cero.
3. Escribir un launch file que orqueste varios nodos.
4. Tener en mente lo que el TP1 y TP2 esperan como salida visual.

## Conceptos que se introducen / se profundizan
- [[Gazebo y rviz]] — RVIZ en profundidad: displays, status, views.
- [[ROS2 - Mensajes]] — composición jerárquica.
- [[ROS2 - Launch Files]] — primera escritura.

## Estructura del tutorial

| Slides | Sección |
|---|---|
| 2–8 | RVIZ: cómo levantarlo, displays, status, views, frames |
| 9–17 | Mensajes complejos: Odometry y Path por dentro |
| 18–23 | Launch File: estructura, package.xml, setup.py, build |
| 24–28 | Preview TP1: resultados esperados (LIDAR, transformaciones, recorrido) |
| 29–31 | Preview TP2: muestreo, modelo odométrico, filtro discreto |

## 1. RVIZ

Detalle completo en [[Gazebo y rviz]]. En resumen, RVIZ es el **visor 3D** de datos publicados en topics; los displays se enchufan a un topic + tipo de mensaje y renderizan según el tipo (lidar como rayos, path como polilínea, robot model como mesh, etc.).

### Workflow recomendado

```bash
# Terminal 1: simulador
ros2 launch turtlebot3_custom_simulation custom_simulation.launch.py

# Terminal 2: RVIZ
rviz2

# Dentro de RVIZ:
# 1) Setear Fixed Frame en "odom" o "map"
# 2) Add → RobotModel (apunta a /robot_description)
# 3) Add → LaserScan, topic /scan
# 4) Add → TF
# 5) Add → Path, topic <el que estés publicando>
```

## 2. Mensajes complejos

Detalle en [[ROS2 - Mensajes]] §3. Ejemplo principal del curso: `nav_msgs/Odometry`.

![[Mensajes - nav msgs Odometry.png]]
*`nav_msgs/Odometry` = header + child_frame_id + PoseWithCovariance + TwistWithCovariance, slide 10.*

La jerarquía completa cuando se descompone:

- `Odometry.pose.pose.position` → `Point` (x, y, z) en metros.
- `Odometry.pose.pose.orientation` → `Quaternion` (x, y, z, w).
- `Odometry.twist.twist.linear` → `Vector3` (m/s).
- `Odometry.twist.twist.angular` → `Vector3` (rad/s).

![[Mensajes - Pose Point Quaternion.png]]
*Point + Quaternion son las "hojas" de Pose, slide 15.*

> [!info] Lectura desde código
> `msg.pose.pose.position.x` — para obtener x; `msg.pose.pose.orientation.{x,y,z,w}` — para el cuaternión. Ver [[Cuaterniones]] §4 para el workflow yaw ↔ cuaternión.

### Path = array de PoseStamped

![[Mensajes - Path PoseStamped.png]]
*Cada elemento del Path es un PoseStamped (header + Pose), slide 17.*

```python
from nav_msgs.msg import Path
from geometry_msgs.msg import PoseStamped

path = Path()
path.header.frame_id = 'map'
for x, y in coords:
    ps = PoseStamped()
    ps.header.frame_id = 'map'
    ps.pose.position.x = x
    ps.pose.position.y = y
    ps.pose.orientation.w = 1.0  # default sin rotación
    path.poses.append(ps)
publisher.publish(path)
```

Esto se renderiza directamente en RVIZ con un display `Path`.

## 3. Launch File

Detalle completo en [[ROS2 - Launch Files]]. Resumen del flujo del tutorial:

```bash
# 1. Crear el directorio
cd ~/ros2_ws/src/example_package
mkdir launch && cd launch
touch example_launch.py
```

```python
# example_launch.py
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(package='example_package', namespace='', executable='talker', name='talker'),
        Node(package='example_package', namespace='', executable='listener', name='listener'),
    ])
```

```xml
<!-- package.xml -->
<exec_depend>ros2launch</exec_depend>
```

```python
# setup.py
import os
data_files=[
    ...
    (os.path.join('share', package_name), ['launch/example_launch.py']),
],
```

```bash
colcon build --symlink-install
source install/setup.bash
ros2 launch example_package example_launch.py
```

## 4. Preview TP1: Transformaciones, Locomoción y Sensado

El TP1 espera que el estudiante produzca tres outputs visuales:

### LIDAR — datos crudos

![[Tutorial 4 - TP1 LIDAR scan.png]]
*Scan del LIDAR: ranges (azul) e intensities (no usadas en este robot). Cada índice es un ángulo del barrido, slide 25.*

### LIDAR — point cloud transformado

![[Tutorial 4 - TP1 LIDAR transformed.png]]
*Izq: point cloud crudo en el frame del lidar. Der: el mismo cloud transformado al frame del mundo, mostrando la pose del robot (verde) y del LIDAR (rojo) sobre los obstáculos detectados, slide 27.*

> Lo que se evalúa: que la composición de transformaciones `world ← robot ← lidar` esté bien aplicada para llevar las mediciones del frame sensor al frame mundo.

### Locomoción — recorrido

![[Tutorial 4 - TP1 robot path.png]]
*El recorrido del robot graficado offline a partir del CSV de odometría — exactamente el ejercicio del [[Tutorial 3 - Interactuando con Gazebo|Tutorial 3]] §5, slide 28.*

## 5. Preview TP2: Modelos Probabilísticos y Filtros Discretos

El TP2 evalúa muestreo + modelo odométrico + filtro discreto.

### Muestreo de Gaussianas

![[Tutorial 4 - TP2 odom model.png]]
*Distribución de posiciones $(x_{t+1}, y_{t+1})$ para 5000 muestras, slide 30.*

> Pregunta del enunciado: "¿Qué está ocurriendo físicamente y qué está interpretando mi algoritmo en consecuencia?". Esto se ataca con [[Modelo de Movimiento (Odometría)|el modelo de odometría]] (M2) y [[Muestreo por Importancia|muestreo]] (M5).

### Filtro discreto: histograma de posiciones

![[Tutorial 4 - TP2 filtro discreto.png]]
*Histograma del belief sobre celdas: la distribución se concentra alrededor de la celda 15-16 después de algunas observaciones, slide 31.*

> Pregunta del enunciado: "¿Qué representan estas probabilidades? ¿A qué instante corresponde?". Esto se ataca con [[Filtros Discretos]] (M5).

## Conexiones
- [[Tutorial 3 - Interactuando con Gazebo]] — tutorial anterior (Gazebo + topics + TF intro).
- [[Tutorial 5 - Bayes]] — siguiente: ejercicios de Bayes.
- [[ROS2 - Launch Files]] / [[Gazebo y rviz]] / [[ROS2 - Mensajes]] — conceptos profundizados.
- [[TP1 - Transformaciones, Locomoción y Sensado]] — primer entregable, anticipado acá.
- [[TP2 - Modelos Probabilísticos y Filtros Discretos]] — segundo entregable, anticipado acá.
- [[Filtros Discretos]] / [[Modelo de Movimiento (Odometría)]] — teoría que respalda el TP2.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf`
  - slides 2–8 → 1. RVIZ
  - slides 9–17 → 2. Mensajes complejos
  - slides 18–23 → 3. Launch File
  - slides 24–28 → 4. Preview TP1
  - slides 29–31 → 5. Preview TP2
