---
modulo: 7. ROS2 y TPs
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf
  - Raw/Diapositivas/Tutoriales/Tutorial 3_ Interactuando con Gazebo.pdf
  - Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# Gazebo y rviz

> Las dos herramientas visuales del stack: **Gazebo** simula físicamente al robot y su entorno (genera datos); **RVIZ** muestra qué "ve" el robot (consume datos publicados en topics). Son complementarias, no alternativas.

![[RVIZ - Gazebo y RVIZ duo.png]]
*Gazebo (simulador, izq) y RVIZ (visualizador, der), slide 3 de T4.*

## 1. Gazebo — simular el robot y el entorno

![[Gazebo - simulacion entorno.png]]
*Gazebo simulando un Turtlebot3 dentro de un mundo con obstáculos cilíndricos; el lidar se ve como rayos azules, slide 27 de T1.*

Gazebo es un **simulador físico** mantenido por Open Robotics. Provee:

- Modelado del robot (URDF) y entorno (mundos `.sdf` o `.world`).
- Física: fricción, colisiones, dinámica de cuerpos rígidos.
- Sensores virtuales: lidar, IMU, cámara, encoders.
- Plugins ROS que publican los datos del robot a los topics estándar (`/scan`, `/odom`, `/imu`, `/joint_states`).

> [!info] Limitación importante
> Gazebo opera con la **GPU**. Necesita al menos 1 GB de VRAM. La interacción varía según arquitectura del CPU (ARM, AMD, Intel) — ARM (Apple Silicon) es la **menos compatible**.

## 2. RVIZ — visualizar lo que el robot ve

![[RVIZ - visualizacion 3D.png]]
*RVIZ mostrando datos del robot: lidar scan, mapa, ejes de coordenadas, path planificado, slide 28 de T1.*

RVIZ no simula nada — es un **visor 3D** de datos publicados en topics ROS. Renderiza un punto, una nube de puntos, una flecha o un mesh basado en lo que llega por un topic.

### Inicializar

![[RVIZ - inicializar interfaz.png]]
*RVIZ recién abierto: panel de Displays (izq), área 3D (centro), panel de Views (der), slide 4 de T4.*

```bash
# Opción 1
rviz2

# Opción 2
ros2 run rviz2 rviz2
```

### Displays

Un **display** es un componente que toma datos de un topic y los renderiza. Se agregan con el botón `Add`:

![[RVIZ - displays add.png]]
*Catálogo de displays disponibles ("By display type") + filtro por topics activos ("By topic"). El tipo de mensaje del display tiene que coincidir con el del topic, slide 5 de T4.*

Tipos comunes en este curso:

| Display | Tipo de mensaje | Uso |
|---|---|---|
| `LaserScan` | `sensor_msgs/LaserScan` | LIDAR scan en 2D/3D |
| `RobotModel` | usa `/robot_description` | Mesh del robot via URDF |
| `TF` | usa el árbol [[ROS2 - TF2]] | Ejes de cada frame |
| `Map` | `nav_msgs/OccupancyGrid` | Mapa de ocupación |
| `Path` | `nav_msgs/Path` | Path planificado o recorrido |
| `Pose` | `geometry_msgs/PoseStamped` | Una pose individual |
| `ParticleCloud` | `nav2_msgs/ParticleCloud` | Nube de partículas (MCL) |
| `Image` | `sensor_msgs/Image` | Cámara |

### Status: OK vs Error

![[RVIZ - displays status.png]]
*Cada display reporta su Status: ✓ verde "Ok" cuando recibe datos válidos; 🛑 rojo "Error" cuando el topic no existe o el TF no está disponible, slide 6 de T4.*

Las causas más comunes de Error:

- El topic no existe (todavía no hay publisher).
- El frame del display no está en el árbol TF.
- El **Fixed Frame** del panel global está mal configurado (debería ser `map` u `odom`).

### Views y Frames

| Tipos de view (perspectiva de cámara) | Tipos de frame |
|---|---|
| Orbit (default) | Fixed Frame: el frame "mundo" en el que se renderiza todo |
| FPS | Target Frame: opcional, hace que la cámara siga este frame |
| Top-Down | |
| XY orbit | |
| Third person | |

> [!warning] El Fixed Frame mata la visualización
> Si el Fixed Frame está seteado a un frame que no existe, **nada se renderiza**. Default razonable: `map` si hay mapa, `odom` si no.

## 3. Workflow combinado

```bash
# Terminal 1: simulador
ros2 launch turtlebot3_custom_simulation custom_simulation.launch.py

# Terminal 2: visualizador
rviz2

# Terminal 3 (opcional): tu nodo de control
ros2 run mi_paquete mi_nodo
```

Lo más común es encapsular las primeras dos terminales en un único [[ROS2 - Launch Files|launch file]] que levante simulador + RVIZ pre-configurado con los displays necesarios.

## Conexiones
- [[ROS2 - Conceptos Base]] — framework.
- [[ROS2 - TF2]] — RVIZ depende de TF para renderizar.
- [[ROS2 - Mensajes]] — los displays se atan a tipos de mensaje.
- [[ROS2 - Launch Files]] — orquestación.
- [[Tutorial 3 - Interactuando con Gazebo]] — práctica con Gazebo + topics.
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — práctica con RVIZ.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf`
  - slide 27 → Gazebo (intro)
  - slide 28 → RVIZ (intro)
- `Raw/Diapositivas/Tutoriales/Tutorial 3_ Interactuando con Gazebo.pdf`
  - slide 2 → Gazebo en detalle
- `Raw/Diapositivas/Tutoriales/Tutorial 4_ msgs, rviz, launch y TP1_TP2.pdf`
  - slide 3 → Gazebo + RVIZ duo
  - slide 4 → 2.1 Inicializar
  - slide 5 → 2.2 Displays
  - slide 6 → 2.3 Status
  - slides 7–8 → 2.4 Views y Frames
