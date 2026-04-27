---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# ROS2 - Conceptos Base

> ROS2 (Robot Operating System 2) es un **middleware** distribuido para sistemas robóticos: en vez de ser un OS, provee herramientas, librerías y un protocolo de comunicación que abstraen la complejidad de programar un robot real.

## 1. ¿Por qué ROS?

Un robot autónomo típico **tiene**: procesador/CPU, ruedas, sensores. Y **quiere**: un algoritmo de control que en todo momento lea sensores, interprete, decida, ejecute movimiento, mida cómo se movió, calcule el mejor camino, y se comunique con una PC externa para monitoreo.

![[ROS - implicaciones complejidad.png]]
*Lo que el código tiene que manejar: interrupciones por sensores, procesamiento, comunicación con hardware, networking, slide 13.*

Sin ROS, esto implica:
- **Threading manual** para no bloquear el algoritmo principal mientras se leen sensores.
- **Comunicación entre computadoras** vía red (a destiempo).
- **Flujo de datos** sensores↔procesador↔actuadores.

![[ROS - lo que ROS resuelve.png]]
*ROS se encarga del threading, la comunicación de red, y el flujo de datos, slide 14.*

## 2. ¿Qué es ROS?

> **Robot Operating System** — no es un OS sino un middleware con herramientas y librerías.

ROS provee:

- **Threading automático** para procesos a destiempo sin bloqueo.
- **Comunicación entre computadoras** vía red.
- **Flujo de datos** entre sensores, actuadores y procesador.
- **Simulación física** de robots y entornos (Gazebo).
- **Visualización** de datos del robot (RVIZ).
- **Grabación** integrada (rosbag) para replay y análisis offline.
- **Paquetes pre-armados** por terceros.
- **Aislamiento** de paquetes para modificaciones.
- **Abstracción de lenguaje** (Python y C++).
- **Comunidad activa**.

![[ROS - features que provee.png]]
*Features que ROS ofrece, slide 18.*

### Arquitectura

Tres componentes nucleares:

| Componente | Rol |
|---|---|
| [[ROS2 - Nodos]] | Proceso ejecutable que corre lógica |
| [[ROS2 - Topics]] | Canal de comunicación entre nodos |
| [[ROS2 - Mensajes]] | Datos estructurados que viajan por los topics |

![[ROS - arquitectura nodes topics.png]]
*Modelo: nodos publican y suscriben a topics; los topics no son sólo punto-a-punto sino que admiten 1-a-N, N-a-1, N-a-N, slide 20.*

### Ejemplo real

Un robot perro corriendo ROS:

![[ROS - ejemplo robot perro.png]]
*Múltiples nodos en computadora a bordo (State Machine, Dyna real Interface, Motors, Joy Node, IMU) más nodos en computadora externa (Teleoperator, Listener), comunicándose por topics tipados, slide 21.*

Más allá de los tres conceptos centrales, ROS2 incluye **servicios, acciones, rosbag, nodos composables, QoS, parámetros, RTO, URDF, capa DDS y variantes, memory allocation, ROS2 tracing, simuladores**, etc. — pero para este curso alcanza con dominar nodos + topics + mensajes.

## 3. El stack del curso

ROS2 tiene varias distribuciones LTS. La materia usa:

![[ROS - stack del curso.png]]
*Versión Humble Hawksbill + simulador Gazebo + paquetes Turtlebot3, slide 25.*

| Pieza | Rol |
|---|---|
| **ROS2 Humble Hawksbill** | Distribución LTS, compatible con Ubuntu 22.04 |
| **Gazebo** | Simulador físico del robot y el entorno |
| **Turtlebot3** | Robot diferencial pequeño, modelo `burger`, paquetes provistos por Open Robotics |
| **RVIZ** | Visor 3D para ver lo que "ve" el robot (pose, lidar, mapas) |

### Comandos básicos

```bash
# Listar nodos activos
ros2 node list

# Listar topics
ros2 topic list

# Ver el tipo de mensaje de un topic
ros2 topic info /scan

# Escuchar mensajes en tiempo real
ros2 topic echo /scan

# Correr un nodo de un paquete
ros2 run <pkg_name> <executable>

# Lanzar un launch file
ros2 launch <pkg_name> <launch_file>.py
```

## Conexiones
- [[ROS2 - Nodos]] — unidad de ejecución.
- [[ROS2 - Topics]] — canal pub/sub.
- [[ROS2 - Mensajes]] — payload tipado.
- [[ROS2 - Publisher]] — lado emisor.
- [[ROS2 - Subscriber]] — lado receptor.
- [[Gazebo y rviz]] — simulación + visualización.
- [[ROS2 - Launch Files]] — arranque múltiple.
- [[Tutorial 1 - Bienvenida e Instalación de ROS2]] — instalación práctica.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf`
  - slides 11–14 → 1. ¿Por qué ROS?
  - slides 16–22 → 2. ¿Qué es ROS?
  - slides 25–28 → 3. El stack del curso
