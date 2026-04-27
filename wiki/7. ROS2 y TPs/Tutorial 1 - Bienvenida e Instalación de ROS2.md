---
modulo: 7. ROS2 y TPs
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# Tutorial 1 — Bienvenida e Instalación de ROS2

> Configurar el entorno de trabajo: instalar ROS2 Humble + Gazebo + Turtlebot3 según OS, validar con un primer launch del simulador.

## Objetivo

Que cada estudiante tenga, **al final del tutorial**:

1. ROS2 Humble corriendo localmente.
2. El paquete `turtlebot3_gazebo` instalado.
3. El comando `ros2 launch turtlebot3_gazebo empty_world.launch.py` levantando una simulación con un Turtlebot3 burger.

## Conceptos que se introducen

- [[ROS2 - Conceptos Base]] — qué es ROS y para qué sirve.
- [[Gazebo y rviz]] — el stack visual del curso.

## Estructura del tutorial

Cinco secciones (slides 9–46):

1. **¿Por qué ROS?** (slides 10–14) — el problema de complejidad sin ROS.
2. **¿Qué es ROS?** (slides 15–23) — definición, arquitectura, ecosistema.
3. **En nuestro caso** (slides 24–28) — Humble + Gazebo + Turtlebot3.
4. **Compatibilidad** (slides 29–31) — Linux preferido, alternativas.
5. **Tarea para la casa** (slides 32–46) — instalación detallada por OS.

Las secciones 1–4 están desarrolladas en [[ROS2 - Conceptos Base]] y [[Gazebo y rviz]]. Esta página enfoca la sección 5: **instalación**.

## Caminos de instalación

Hay dos rutas oficiales, según OS:

### A. Robostack (recomendado, multiplataforma)

Robostack es un set de paquetes Conda mantenido por Open Robotics. Funciona en Ubuntu, macOS y Windows con un único comando.

![[Robostack - instalacion comando.png]]
*Comando único para crear el environment de Robostack con todos los paquetes Turtlebot3 + ROS2 Humble, slide 37.*

```bash
# 1. Instalar Miniforge (https://conda-forge.org/download/)
# 2. Crear el environment Robostack:
mamba create -n rosenv -c conda-forge -c robostack-staging \
  ros-humble-desktop ros-humble-turtlebot3 compilers cmake \
  pkg-config make ninja colcon-common-extensions catkin_tools \
  rosdep ros-humble-turtlebot3-bringup \
  ros-humble-turtlebot3-cartographer \
  ros-humble-turtlebot3-description ros-humble-turtlebot3-example \
  ros-humble-turtlebot3-msgs ros-humble-turtlebot3-navigation2 \
  ros-humble-turtlebot3-node ros-humble-turtlebot3-teleop \
  ros-humble-turtlesim ros-humble-turtlebot3-gazebo \
  ros2-distro-mutex==0.5.0

# 3. Activar
mamba activate rosenv

# 4. Probar la instalación
rviz2

# 5. Prueba de fuego
export TURTLEBOT3_MODEL=burger
ros2 launch turtlebot3_gazebo empty_world.launch.py
```

> [!warning] Cuidado con los breaklines al copiar
> El `mamba create` es **una sola línea de código**, los saltos en el slide son por ancho del cuadro. Si copiás roto se rompe el comando.

### B. ROS2 crudo en Ubuntu

Para Ubuntu 22.04 sin Conda — instalación directa desde los repos de Open Robotics. Más liviana, pero atada al OS.

![[ROS2 - workspace turtlebot3.png]]
*Workspace de Turtlebot3 con `colcon build` + sourcing del bashrc + `export TURTLEBOT3_MODEL=burger`, slide 46 (en Robostack).*

Pasos resumidos:

```bash
# Asumiendo que ya seguiste los pasos para instalar el ws del turtlebot3:
cd ~/turtlebot3_ws/src
# (descargar turtlebot_custom_simulation.zip del campus, descomprimir aquí)
cd ~/turtlebot3_ws
colcon build --symlink-install
echo 'source ~/turtlebot3_ws/install/setup.bash' >> ~/.bashrc
echo 'export TURTLEBOT3_MODEL=burger' >> ~/.bashrc
source ~/.bashrc

# Lanzar la simulación con piso blanco (custom)
ros2 launch turtlebot3_custom_simulation custom_simulation.launch.py
```

> [!info] Si usás zsh u otra shell
> Reemplazá `~/.bashrc` por `~/.zshrc` (o lo que use tu shell — verificá con `echo $SHELL`).

## Compatibilidad por OS

| OS | Recomendación |
|---|---|
| **Ubuntu 22.04** | ROS2 crudo o Robostack — ambos andan |
| **Ubuntu 24** | Robostack puede fallar — usar 22.04 |
| **macOS Intel** | Robostack |
| **macOS Apple Silicon (M1+)** | Robostack (puede fallar — hablar con Bauti) |
| **Windows** | Robostack o WSL (puede fallar). Plan B: partición Ubuntu, computadoras del laboratorio, o "se hacen amigos de alguien que le ande" |

## Validación

El tutorial está completo cuando se puede correr:

```bash
export TURTLEBOT3_MODEL=burger
ros2 launch turtlebot3_gazebo empty_world.launch.py
```

…y se abre Gazebo mostrando un Turtlebot3 burger en un entorno vacío. A partir de ese punto, el resto de los tutoriales asume entorno funcional.

## Conexiones
- [[ROS2 - Conceptos Base]] — secciones 1–2 del tutorial.
- [[Gazebo y rviz]] — sección 3.
- [[Tutorial 2 - Speaker and Listener]] — siguiente tutorial, primer programa pub/sub.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 1_ Bienvenida e Instalación de ROS2.pdf`
  - slides 1–7 → Estructura de la cursada
  - slides 9–14 → 1. ¿Por qué ROS?
  - slides 15–23 → 2. ¿Qué es ROS?
  - slides 24–28 → 3. En nuestro caso
  - slides 29–31 → 4. Compatibilidad
  - slides 32–46 → 5. Tarea para la casa: instalación
