---
modulo: 7. ROS2 y TPs
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# ROS2 - Conceptos Base

> Framework de middleware para sistemas robóticos con arquitectura distribuida basada en nodos.

## ¿Qué es ROS2?

ROS2 (Robot Operating System 2) es un framework que provee herramientas y librerías para construir sistemas robóticos. No es un sistema operativo propiamente dicho, sino un conjunto de herramientas que facilitan la comunicación entre procesos, la gestión de dispositivos, y el manejo de datos en tiempo real.

Versiones relevantes:
- **Humble Hawksbill** — LTS (Long Term Support), versión usada en esta cursada
- **Foxy Fitzroy** — LTS anterior

### Arquitectura  *(Tutorial 2, págs. 1–4)*

ROS2 se basa en un patrón de **comunicación distribuida** donde cada proceso funciona como un **nodo** independiente:

```
[ Nodo Publisher ] --(topic)--> [ Nodo Subscriber ]
```

Los componentes fundamentales son:

| Componente | Descripción |
|---|---|
| [[ROS2 - Nodos]] | Unidad de ejecución (proceso) |
| [[ROS2 - Topics]] | Canal de comunicación asíncrono pub/sub |
| [[ROS2 - Mensajes]] | Tipos de datos estructurados |
| [[ROS2 - Publisher]] | Nodo que emite mensajes a un topic |
| [[ROS2 - Subscriber]] | Nodo que recibe mensajes de un topic |

### Workspace y construcción  *(Tutorial 1, págs. 9–14)*

Todo proyecto ROS2 vive dentro de un **workspace**:

```
mi_ws/
├── src/              ← código fuente (paquetes)
├── build/            ← artifacts de compilación
├── install/          ← instalación resultante
└── log/              ← logs de construcción
```

Pasos para construir:
```bash
# Crear workspace
mkdir -p mi_ws/src
cd mi_ws

# Construir todos los paquetes en src/
colcon build

# Sourcear el entorno
source install/setup.bash
```

El comando `colcon build` compila y enlaza todos los paquetes. El `source install/setup.bash` exporta las variables de entorno necesarias para encontrar ejecutables, mensajes, etc.

### Comandos útiles  *(Tutorial 1, págs. 15–18)*

```bash
# Listar nodos activos
ros2 node list

# Listar topics
ros2 topic list

# Ver tipo de mensaje de un topic
ros2 topic info /topic_name

# Ver publicaciones en tiempo real
ros2 topic echo /topic_name
```

## Conexiones con el curso
- [[Tutorial 1 - Bienvenida e Instalación de ROS2]] — instalación práctica
- [[Tutorial 2 - Speaker and Listener]] — primer programa pub/sub
- [[Tutorial 3 - Interactuando con Gazebo]] — simulación

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf`
  - págs. 1–8 → ¿Qué es ROS2?
- `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf`