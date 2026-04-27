---
modulo: 7. ROS2 y TPs
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# ROS2 - Publisher

> Lado emisor de la comunicación pub/sub: un objeto creado dentro de un [[ROS2 - Nodos|nodo]] que escribe mensajes a un [[ROS2 - Topics|topic]] cuando lo invocan. Típicamente disparado por un **timer con callback**, no por un loop.

## Prerequisitos
- [[ROS2 - Nodos]] — el publisher vive dentro de un nodo.
- [[ROS2 - Topics]] — el publisher escribe a un topic.
- [[ROS2 - Mensajes]] — el publisher está atado a un tipo.

## 1. API básica

```python
self.publisher = self.create_publisher(MsgType, 'topic_name', queue_size)
```

Tres argumentos:

- **`MsgType`** — la clase del mensaje (ej. `std_msgs.msg.String`).
- **`'topic_name'`** — nombre del topic (ej. `'chatter'`).
- **`queue_size`** — buffer de envío (típicamente `10`).

## 2. El patrón típico: timer + callback

Un publisher rara vez se invoca a mano — lo más común es disparar `publish()` desde un **callback de timer**, así el nodo no bloquea (ver [[ROS2 - Nodos]] §1).

![[Talker - codigo completo.png]]
*Código completo del nodo Talker: import, clase TalkerNode con publisher + timer, callback, main, slide 13.*

### Análisis línea por línea

#### Crear el publisher

![[Talker - create publisher.png]]
*`self.create_publisher(String, 'chatter', 10)` — tipo + nombre del topic + queue size, slide 17.*

```python
self.publisher = self.create_publisher(String, 'chatter', 10)
```

Esto registra al nodo como publisher del topic `chatter` con tipo `std_msgs/String`. Desde este punto, cualquier subscriber que se conecte al mismo topic con el mismo tipo va a recibir lo que publiquemos.

#### Crear el timer

![[Talker - create timer.png]]
*`self.create_timer(1, self.timer_callback)` — período en segundos + función a invocar, slide 18.*

```python
self.timer = self.create_timer(1, self.timer_callback)  # cada 1 segundo
```

#### El callback

```python
def timer_callback(self):
    msg = String()
    msg.data = f'hello world {self.get_clock().now().to_msg().sec}'
    self.get_logger().info(msg.data)
    self.publisher.publish(msg)
```

Cada segundo: arma un mensaje `String`, lo loggea, lo publica. Entre callbacks el nodo está **libre** — puede recibir otros mensajes, exponer servicios, etc.

## 3. Cuándo NO usar timer

A veces se publica como respuesta a un evento (ej. cada vez que llega una observación nueva, dispara una decisión). En ese caso el publisher se invoca desde el callback del subscriber relacionado, no de un timer propio. Pero el timer es el patrón **default** para cualquier flujo periódico (telemetría, comandos de velocidad, status).

## Conexiones
- [[ROS2 - Nodos]] — el publisher vive en un nodo.
- [[ROS2 - Topics]] — escribe a un topic.
- [[ROS2 - Mensajes]] — está tipado.
- [[ROS2 - Subscriber]] — lado receptor (espejo del publisher).
- [[Tutorial 2 - Speaker and Listener]] — implementación completa del talker.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf`
  - slide 13 → 1. API + código completo
  - slide 17 → 2.1 Crear el publisher
  - slide 18 → 2.2 Crear el timer
  - slide 19 → 2.3 El callback
