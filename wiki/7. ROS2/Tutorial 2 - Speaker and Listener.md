---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# Tutorial 2 — Speaker and Listener

> Primer programa ROS2: dos nodos (un **talker** que publica y un **listener** que recibe) comunicándose por un topic. Cubre el ciclo completo: estructura del workspace, creación del paquete, código de los nodos, build y run. Cierra con un anticipo de TP1: representaciones de rotación 3D.

## Objetivo

Construir un paquete ROS2 desde cero con dos nodos:

- `talker` — publica un `String` cada segundo al topic `chatter`.
- `listener` — se suscribe a `chatter` y loggea cada mensaje recibido.

## Conceptos que se introducen

- [[ROS2 - Nodos]] — el patrón timer + callback.
- [[ROS2 - Topics]] — el modelo pub/sub.
- [[ROS2 - Mensajes]] — `std_msgs/String`.
- [[ROS2 - Publisher]] — `create_publisher`.
- [[ROS2 - Subscriber]] — `create_subscription`.

## Estructura del tutorial

| Slides | Sección |
|---|---|
| 4–20 | Talker: del problema del `while True` al patrón timer + código completo |
| 21–27 | Workspace y creación del paquete |
| 28–37 | Listener: subscriber + callback + correr ambos |
| 40–56 | **TP1: Representaciones de rotación 3D** (Euler, Matriz, Cuaterniones) |

## 1. Workspace y paquete

![[Workspace - estructura completa.png]]
*Estructura del workspace ROS2: src (código fuente), build/install/log (artefactos generados). Cada paquete tiene `package.xml`, `setup.py`, `setup.cfg` y subdirectorios para módulos Python, slide 22.*

```
mi_ws/
├── src/                 ← código fuente (paquetes)
│   └── pkg_name/
│       ├── package.xml
│       ├── setup.py
│       ├── setup.cfg
│       └── pkg_name/
│           ├── __init__.py
│           └── my_node.py
├── build/               ← artefactos de compilación
├── install/             ← instalación resultante
└── log/                 ← logs de construcción
```

### Pasos completos para crear el paquete

![[Workspace - pasos crear paquete.png]]
*Pasos 1–8: crear ws, crear paquete, escribir código, modificar package.xml/setup.py, compilar, sourcear, correr, slide 27.*

```bash
# 1. Crear workspace + carpeta src
mkdir -p ~/Documents/ros2_ws/src && cd ~/Documents/ros2_ws/src

# 2. Crear paquete
ros2 pkg create --build-type ament_python --license Apache-2.0 <pkg_name>

# 3. Entrar al directorio del módulo
cd <pkg_name>/<pkg_name>

# 4. Crear el código del nodo (talker.py, listener.py)

# 5. Modificar package.xml y setup.py (ver abajo)

# 6. Compilar (desde el ws)
cd ~/Documents/ros2_ws
colcon build --symlink-install

# 7. Sourcear el entorno
source install/setup.bash

# 8. Correr el nodo
ros2 run <pkg_name> talker
```

### `package.xml` y `setup.py`

En **`package.xml`** se agregan después de la licencia:

```xml
<exec_depend>rclpy</exec_depend>
<exec_depend>std_msgs</exec_depend>
```

En **`setup.py`**, dentro de `entry_points` → `console_scripts`, se declaran los ejecutables:

```python
entry_points={
    'console_scripts': [
        'talker = pkg_name.my_file:main',
        'listener = pkg_name.my_other_file:main',
    ],
},
```

## 2. Talker (publisher con timer)

Ver [[ROS2 - Publisher]] para el detalle del patrón. Resumen:

- `TalkerNode(Node)` con `super().__init__('talker')`.
- `self.create_publisher(String, 'chatter', 10)` registra el publisher.
- `self.create_timer(1, self.timer_callback)` dispara cada segundo.
- En el callback se arma el `String`, se loggea y se publica.

## 3. Listener (subscriber con callback)

Ver [[ROS2 - Subscriber]] para el detalle. Resumen:

- `ListenerNode(Node)` con `super().__init__('listener')`.
- `self.create_subscription(String, 'chatter', self.callback, 10)` registra el listener.
- El `callback(self, msg)` se invoca con cada mensaje y loggea `msg.data`.

## 4. Correr ambos

![[Listener - setup correr ambos.png]]
*setup.py con ambos entry points + colcon build + correr en dos terminales distintas, slide 37.*

```bash
# Terminal 1
ros2 run pkg_name talker

# Terminal 2
ros2 run pkg_name listener
```

### Preguntas reflexivas

- **¿Qué pasa si sólo corremos el listener?** El topic existe (lo declara el subscriber), pero no llegan mensajes. El listener queda esperando, sin error.
- **¿Qué tópicos veo si corro la simulación del Turtlebot?** Mucho más que `/chatter` — `/odom`, `/cmd_vel`, `/scan`, `/tf`, etc. — todos los que publican los nodos del robot simulado.

## 5. Anticipo de TP1: representaciones de rotación 3D

El tramo final de T2 (slides 40–56) prepara el terreno para [[TP1 - Transformaciones, Locomoción y Sensado]] cubriendo cómo representar orientaciones 3D. Este contenido se desarrolla en M1:

- [[Rotaciones]] — tabla comparativa Euler / Matriz / Cuaterniones, con PRO y CON.
- [[Cuaterniones]] — representación con 4 números, sin singularidades, ideal para control y EKF.

> [!info] Por qué importa para el TP
> El robot publica su orientación como **cuaternión** en `/odom`. Para hacer control en yaw (giro 2D) hay que convertir cuaternión → ángulo de Euler (yaw). El TP1 te obliga a hacer ese pipeline.

## Conexiones
- [[Tutorial 1 - Bienvenida e Instalación de ROS2]] — tutorial anterior.
- [[ROS2 - Nodos]] / [[ROS2 - Publisher]] / [[ROS2 - Subscriber]] — conceptos profundizados.
- [[Rotaciones]] / [[Cuaterniones]] — fundamento teórico para TP1.
- [[TP1 - Transformaciones, Locomoción y Sensado]] — primera aplicación.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf`
  - slides 4–20 → Talker (cubierto en [[ROS2 - Publisher]] y [[ROS2 - Nodos]])
  - slides 21–27 → 1. Workspace y paquete
  - slides 28–37 → 3–4. Listener y correr ambos
  - slides 38–39 → 4.1 Preguntas reflexivas
  - slides 40–56 → 5. Anticipo TP1 (cubierto en [[Rotaciones]] y [[Cuaterniones]])
