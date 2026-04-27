---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# ROS2 - Subscriber

> Lado receptor de la comunicación pub/sub: un objeto creado dentro de un [[ROS2 - Nodos|nodo]] que **registra un callback** para que ROS lo invoque cada vez que llegue un mensaje a un [[ROS2 - Topics|topic]]. A diferencia del [[ROS2 - Publisher]], **no usa timer** — reacciona a eventos.

## Prerequisitos
- [[ROS2 - Nodos]] — el subscriber vive dentro de un nodo.
- [[ROS2 - Topics]] — el subscriber escucha un topic.
- [[ROS2 - Mensajes]] — el subscriber está tipado.
- [[ROS2 - Publisher]] — la contraparte del subscriber.

## 1. ¿Por qué no timer?

Un publisher periódico necesita timer porque "decide solo" cuándo emitir. Un subscriber **no decide cuándo recibir** — es ROS quien le entrega un mensaje cada vez que llega al topic.

![[Listener - callback evento.png]]
*El subscriber expone un callback `receive(msg)` que ROS invoca con cada mensaje nuevo, slide 33.*

El ciclo es **reactivo**: ROS recibe el mensaje del topic → busca subscribers registrados → invoca su callback con el mensaje como argumento.

## 2. API básica

```python
self.subscription = self.create_subscription(
    MsgType,        # tipo
    'topic_name',   # nombre del topic
    self.callback,  # función a invocar
    queue_size      # buffer de recepción (típicamente 10)
)
```

## 3. Código completo del Listener

![[Listener - codigo completo.png]]
*`ListenerNode` completo: subscription en init, callback que loggea, main estándar, slide 34.*

```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class ListenerNode(Node):
    def __init__(self):
        super().__init__('listener')
        self.subscription = self.create_subscription(
            String,
            'chatter',
            self.callback,
            10
        )
        self.subscription  # evita warning de variable no usada

    def callback(self, msg):
        self.get_logger().info(f"I heard: {msg.data}")

def main():
    rclpy.init()
    node = ListenerNode()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    finally:
        node.destroy_node()
        rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Crear la subscription

![[Listener - create subscription.png]]
*La invocación a `create_subscription` con sus 4 argumentos, slide 35.*

El subscriber se registra al `'chatter'` de tipo `String` con el callback `self.callback`. ROS arma internamente la conexión vía DDS.

## 4. Detalles importantes

> [!info] El subscriber funciona aunque no haya publishers
> Si corremos sólo el listener (`ros2 run pkg listener`) sin el talker, el listener queda esperando — no falla. ROS no garantiza que haya un publisher conectado. El topic existe en cuanto alguien lo declara (publisher o subscriber); los mensajes empiezan a fluir cuando aparecen ambos lados.

> [!warning] Queue size importa
> Con `queue_size=10`, si el publisher emite más rápido de lo que el subscriber procesa, los mensajes viejos se descartan. Para datos críticos (sensores) se ajusta más alto; para datos ruidosos puede convenir bajar para descartar lo viejo.

## Conexiones
- [[ROS2 - Nodos]] — el subscriber vive en un nodo.
- [[ROS2 - Topics]] — escucha un topic.
- [[ROS2 - Mensajes]] — está tipado.
- [[ROS2 - Publisher]] — la contraparte emisora.
- [[Tutorial 2 - Speaker and Listener]] — implementación completa del listener.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf`
  - slides 31–33 → 1. ¿Por qué no timer?
  - slide 34 → 3. Código completo del Listener
  - slide 35 → 3.1 Crear la subscription
  - slides 38–39 → 4. Detalles
