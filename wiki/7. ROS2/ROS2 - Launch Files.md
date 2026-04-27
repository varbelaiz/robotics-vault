---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# ROS2 - Launch Files

> Archivos Python (`*.launch.py`) que **orquestan el arranque de múltiples [[ROS2 - Nodos|nodos]] con configuración**: parámetros, remapeos de topics, namespaces, simulador + visualizador todo de una. Sin launch files cada nodo se levanta a mano en su propia terminal.

## Prerequisitos
- [[ROS2 - Conceptos Base]] — el comando `ros2 launch`.
- [[ROS2 - Nodos]] — lo que se lanza.

## 1. Estructura mínima

Un launch file es un script Python que define una función **`generate_launch_description()`** que devuelve una `LaunchDescription` con la lista de acciones (nodos a levantar, otros launch files a incluir, comandos shell, etc.).

![[Launch - launch.py codigo.png]]
*Launch file de ejemplo: dos nodos (`talker` + `listener`) declarados como `Node(...)` y devueltos en una `LaunchDescription`, slide 20 de T4.*

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    talker_node = Node(
        package='example_package',
        namespace='',
        executable='talker',
        name='talker'
    )

    listener_node = Node(
        package='example_package',
        namespace='',
        executable='listener',
        name='listener'
    )

    return LaunchDescription([
        talker_node,
        listener_node,
    ])
```

### Argumentos de `Node(...)`

| Campo | Significado |
|---|---|
| `package` | nombre del paquete ROS2 |
| `executable` | entry point declarado en `setup.py` |
| `name` | nombre que el nodo registra en el grafo (puede sobrescribir el del código) |
| `namespace` | prefijo para todos los topics del nodo |
| `parameters` | lista de dicts o YAMLs |
| `remappings` | lista de tuplas `('topic_original', 'topic_nuevo')` |

> [!info] Namespace + remapping
> Si un nodo declara que publica a `chatter` y le ponés `namespace='/robot1'`, el topic real es `/robot1/chatter`. Si dos copias del mismo nodo viven en namespaces distintos, sus topics no colisionan.

![[Launch - package.xml setup.py.png]]
*Ajustes mínimos: `<exec_depend>ros2launch</exec_depend>` en package.xml + entrada `data_files` apuntando a `launch/example_launch.py` en setup.py, slide 22 de T4.*

## 2. Pasos para crear un launch file

```bash
# 1. Crear el directorio launch dentro del paquete
cd ~/ros2_ws/src/example_package
mkdir launch
cd launch
touch example_launch.py
```

Luego se edita `example_launch.py` con el contenido del paso 1.

### `package.xml`

Agregar después de la licencia:

```xml
<exec_depend>ros2launch</exec_depend>
```

### `setup.py`

En `data_files`, declarar el directorio `launch` para que se instale:

```python
import os

data_files=[
    ...
    (os.path.join('share', package_name), ['launch/example_launch.py']),
],
```

### Build y lanzamiento

![[Launch - colcon build run.png]]
*`colcon build --symlink-install`, source y `ros2 launch <pkg> <file.launch.py>`, slide 23 de T4.*

```bash
# Desde el ws root
colcon build --symlink-install
source install/setup.bash
ros2 launch example_package example_launch.py
```

## 3. Casos de uso típicos en el curso

| Combinación | Por qué |
|---|---|
| Gazebo + RVIZ + nodo del estudiante | Alinear simulador, visualizador y código de control con un solo comando |
| Múltiples nodos del estudiante | TP3 (MCL): nodo de partículas + nodo de evaluación + recorder, todo junto |
| Otro launch + parámetros | `IncludeLaunchDescription(...)` permite componer un launch que incluye `turtlebot3_gazebo`'s default y agrega encima nodos propios |

> [!warning] Si no se ve el launch file después del build
> Es porque falta declararlo en `setup.py` `data_files`, o el `colcon build` corrió antes de agregarlo. Volver a buildear y sourcear.

## Conexiones
- [[ROS2 - Conceptos Base]] — el comando `ros2 launch`.
- [[ROS2 - Nodos]] — lo que se lanza.
- [[Gazebo y rviz]] — un launch típico levanta simulador + visualizador.
- [[ROS2 - TF2]] — los static publishers se suelen lanzar desde un launch file.
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — práctica de creación.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf`
  - slide 19 → 2. Crear directorio launch
  - slides 20–21 → 1. Estructura mínima
  - slide 22 → 2. package.xml + setup.py
  - slide 23 → 2. Build y lanzamiento
