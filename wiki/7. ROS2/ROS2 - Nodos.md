---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# ROS2 - Nodos

> Un **nodo** es un proceso ejecutable independiente que participa en el grafo ROS2: corre lógica, publica y suscribe a topics, expone servicios. Es la unidad mínima de modularidad.

## Prerequisitos
- [[ROS2 - Conceptos Base]] — qué es ROS2 y por qué.

## 1. Idea del nodo

Cada nodo:
- Tiene un **nombre único** dentro del grafo.
- Puede **publicar y/o suscribirse** a topics.
- Tiene un **ciclo de vida** (init → spin → shutdown).
- **No bloquea** el grafo: ROS espera que cada nodo ocupe el menor tiempo posible.

### El problema del `while True`

Pensemos en un "talker" — un nodo que publica mensajes cada segundo. La solución ingenua sería:

![[Talker - nodo continuo.png]]
*Un nodo `talker` publica continuamente al topic `chatter`, slide 4.*

```python
import time
while True:
    time.sleep(1)
    publish()
```

Pero este nodo **está ocupado el 100% del tiempo** desde el punto de vista de ROS:

![[Talker - while True bloqueante.png]]
*Si el nodo bloquea con `while True + sleep`, no puede escuchar otros tópicos ni correr otros procesos, slide 7.*

ROS quiere lo contrario: que el nodo ocupe el menor tiempo posible, dejando el resto del CPU libre.

### La solución: `wall_timer` con callback

ROS provee un **timer** que dispara una función callback periódicamente:

![[Talker - wall timer pattern.png]]
*Pattern del wall_timer: cada 1 segundo se ejecuta el callback, que llama a publish(). Entre callbacks el nodo está libre, slide 10.*

Ahora el nodo está ocupado **sólo unas milésimas cada segundo**, en vez de bloquear continuo.

## 2. Estructura mínima en Python

```python
import rclpy
from rclpy.node import Node

class MiNode(Node):
    def __init__(self):
        super().__init__('mi_nodo')
        # crear publishers, subscribers, timers, etc.

def main():
    rclpy.init()
    nodo = MiNode()
    try:
        rclpy.spin(nodo)
    except KeyboardInterrupt:
        pass
    finally:
        nodo.destroy_node()
        rclpy.shutdown()

if __name__ == '__main__':
    main()
```

Las cuatro fases obligatorias:

1. `rclpy.init()` — inicializa la librería ROS para Python.
2. `Node(...)` — crea la instancia, registra el nombre en el grafo.
3. `rclpy.spin(nodo)` — bucle que mantiene el nodo procesando callbacks.
4. `destroy_node()` + `rclpy.shutdown()` — limpieza ordenada.

## 3. Roles del nodo

Un mismo nodo puede combinar varios roles simultáneamente:

- **[[ROS2 - Publisher|Publisher]]** — emite mensajes a un topic.
- **[[ROS2 - Subscriber|Subscriber]]** — recibe mensajes de un topic.
- **Timer** — dispara callbacks en intervalos fijos.
- **Service / Action** — RPC sincrónico/asincrónico (fuera del alcance de la cursada base).

## Conexiones
- [[ROS2 - Conceptos Base]] — el nodo es uno de los 3 conceptos centrales.
- [[ROS2 - Topics]] — canales por los que el nodo se comunica.
- [[ROS2 - Publisher]] — rol emisor de un nodo.
- [[ROS2 - Subscriber]] — rol receptor de un nodo.
- [[Tutorial 2 - Speaker and Listener]] — primer ejemplo práctico (talker + listener).

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf`
  - slides 4–7 → 1.1 El problema del `while True`
  - slides 9–12 → 1.2 La solución: `wall_timer` con callback
  - slides 13–20 → 2. Estructura mínima en Python
