---
modulo: 7. ROS2 y TPs
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# ROS2 - Nodos

> Unidad básica de ejecución en ROS2: cada nodo es un proceso independiente.

## Definición  *(Tutorial 2, págs. 1–4)*

Un **nodo** es un proceso ejecutable que participa en la red ROS2. Cada nodo:
- Tiene un nombre único dentro del grafo
- Puede publicar, suscribir, o hacer ambas cosas simultáneamente
- Comunica con otros nodos a través de topics, servicios, o acciones

### Estructura mínima de un nodo Python  *(Tutorial 2, págs. 5–8)*

```python
import rclpy
from rclpy.node import Node

class MiNode(Node):
    def __init__(self):
        super().__init__('mi_nodo')

def main(args=None):
    rclpy.init(args=args)
    nodo = MiNode()
    rclpy.spin(nodo)
    nodo.destroy_node()
    rclpy.shutdown()
```

Cada nodo requiere:
1. `rclpy.init()` — inicializa la librería
2. `Node()` — crea la instancia
3. `rclpy.spin()` — mantiene el nodo activo, procesando callbacks
4. `shutdown()` — limpieza

### Nomenclatura  *(Tutorial 2, págs. 9–10)*

Los nodos pueden tener **namespaces** y **nombres remapeados**:

```bash
# Remapear nombre al ejecutar
ros2 run pkg nodo --ros-args -r __node:=nuevo_nombre

# Namespace para aislar
ros2 run pkg nodo --ros-args -r __ns:=/mi_namespace
```

## Conexiones
- [[ROS2 - Conceptos Base]] — componente del framework
- [[ROS2 - Topics]] — canales de comunicación entre nodos
- [[ROS2 - Publisher]] — rol del nodo como emisor
- [[ROS2 - Subscriber]] — rol del nodo como receptor

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf`
