---
modulo: 8. TPs
estado: completo
fuentes:
  - Raw/TPs/Enunciado TP2.pdf
ultima_actualizacion: 2026-04-27
---

> [[TPs|← TPs]] | [[Robotica|← Inicio]]

# TP2 — Modelos Probabilísticos y Filtros Discretos

> Cuatro bloques: (1) implementar **3 muestreadores** de gaussiana desde uniformes (suma de 12, rechazo, Box-Muller), (2) implementar el **modelo de movimiento basado en odometría** con ruido y evaluar 5000 muestras, (3) llevarlo a **ROS2** con visualización de partículas en RVIZ, (4) implementar un **filtro de Bayes discreto** sobre un mundo 1D de 20 celdas.

## Objetivo

Que el estudiante pueda:

1. Generar muestras gaussianas a partir de uniformes con tres métodos distintos y comparar tiempos de ejecución (`timeit`).
2. Implementar `sample_motion_model_odometry` y evaluar visualmente la distribución resultante.
3. Integrar el modelo en un nodo ROS2 que publica una nube de partículas (visible en RVIZ).
4. Implementar el filtro discreto del [[Tutorial 6 - Movimiento, Sensores e Histogramas|Tutorial 6]] sobre un mundo 1D acotado.

## Conceptos que necesitás

- [[Variables Aleatorias Continuas]] — muestreo de gaussianas.
- [[Gaussiana Multivariada]] — para entender qué se está muestreando.
- [[Muestreo por Importancia]] — el método de rechazo es un caso particular.
- [[Modelo de Movimiento (Odometría)]] — el modelo $P(x_t \mid u_t, x_{t-1})$ basado en deltas.
- [[Filtros Discretos]] — el filtro del bloque 4.
- [[Filtro de Bayes]] — base teórica del filtro discreto.
- [[ROS2 - Launch Files]] — el TP provee `launch_my_odometry.launch.py` con argumento `alpha`.
- [[ROS2 - Mensajes]] — la nube de partículas se publica como `geometry_msgs/PoseArray` o similar.

## Pasos del enunciado

### 1. Muestreo de distribuciones de probabilidad

Implementar tres funciones que generen muestras de $\mathcal{N}(\mu, \sigma^2)$ usando **sólo** muestras uniformes:

| # | Método | Descripción |
|---|---|---|
| 1 | **Suma de 12 uniformes** | Sumar 12 $u_i \sim U(0, 1)$, restar 6 → aprox. gaussiana por TCL. |
| 2 | **Muestreo con rechazo** | Generar $(x, y)$ uniformes; aceptar si $y < f(x)$ donde $f$ es la PDF target. |
| 3 | **Box-Muller** | $x = \cos(2\pi u_1)\sqrt{-2 \log u_2}$, con $u_1, u_2 \sim U(0,1)$. |

Comparar tiempos de ejecución con `timeit`. Comparar también contra `numpy.random.normal`.

> [!info] Cuál esperar más rápido
> `numpy.random.normal` está vectorizado en C — orden de magnitud más rápido que cualquier impl Python pura. Entre los tres methods Python, Box-Muller suele ganar (sólo 2 uniformes y un par de operaciones).

### 2. Modelo de movimiento basado en odometría

Implementar `sample_motion_model_odometry(x_t, u_t, alpha)` con:

$$\mathbf{x}_t = \begin{pmatrix} x \\ y \\ \theta \end{pmatrix}, \quad \mathbf{u}_t = \begin{pmatrix} \delta_{r1} \\ \delta_{r2} \\ \delta_t \end{pmatrix}, \quad \boldsymbol{\alpha} = \begin{pmatrix} \alpha_1 \\ \alpha_2 \\ \alpha_3 \\ \alpha_4 \end{pmatrix}$$

La función devuelve $\mathbf{x}_{t+1}$ aplicando los deltas con ruido gaussiano (varianza dada por $\alpha$). Detalle del algoritmo en [[Tutorial 6 - Movimiento, Sensores e Histogramas|Tutorial 6 §1]].

**Evaluación**: 5000 muestras con

$$\mathbf{x}_t = (2.0, 4.0, 0.0)^T, \quad \mathbf{u}_t = (\pi/2, 0.0, 1.0)^T, \quad \boldsymbol{\alpha} = (0.1, 0.1, 0.01, 0.01)^T$$

Graficar todas las posiciones $(x_{t+1}, y_{t+1})$ en un solo scatter plot. *(Pista: la nube debería formar un arco — el preview está en [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] §5).*

### 3. Modelo en ROS2

Paquete provisto: `tp2`. Pasos:

```bash
# Terminal 1: simulador
ros2 launch turtlebot3_custom_simulation custom_simulation.launch.py

# Terminal 2: nodo del modelo de odometría (con default alpha)
ros2 launch tp2 launch_my_odometry.launch.py

# Terminal 3: teleop para mover el robot
ros2 run turtlebot3_teleop teleop_keyboard
```

![[TP2 - rviz particulas odometria.png]]
*RVIZ muestra el robot con su nube de partículas en rojo: cada partícula es una muestra del modelo de odometría. Las partículas duran un tiempo antes de desaparecer (decay), por eso se ven varias instancias acumuladas, slide 3 del enunciado.*

Defaults: $\alpha_1 = \alpha_2 = 0.2$, $\alpha_3 = \alpha_4 = 0.02$. **Probar** distintos valores con:

```bash
ros2 launch tp2 launch_my_odometry.launch.py alpha:="[a, b, c, d]"
```

> [!info] Qué mirar al variar alpha
> $\alpha_1, \alpha_2$ controlan ruido en las **rotaciones** ($\delta_{r1}, \delta_{r2}$). Subirlos hace que la nube se **abra angularmente** (los puntos se dispersan en arco). $\alpha_3, \alpha_4$ controlan el ruido en la **traslación** ($\delta_t$). Subirlos extiende la nube **radialmente**.

### 4. Filtro Discreto

Mundo 1D acotado de **20 celdas**, robot inicialmente en celda 10. Modelo de movimiento:

| Comando | Resultado |
|---|---|
| "Avanzar un paso" | 25% no se mueve, 50% +1, 25% +2 (sólo hacia adelante) |
| "Retroceder un paso" | Espejo (sólo hacia atrás) |

> [!warning] Borde derecho
> Si el robot está en la **última** celda y trata de avanzar: 100% se queda. Si está en la **penúltima**: 25% se queda, 75% avanza una. Idem en el borde izquierdo.

Implementar:

```python
import numpy as np
bel = np.hstack((np.zeros(10), 1, np.zeros(9)))  # belief inicial: certeza en celda 10
# Ejecutar 9× "avanzar un paso"
# Después 3× "retroceder un paso"
# Graficar belief final
```

Detalle del algoritmo en [[Filtros Discretos]] (M5) y [[Tutorial 6 - Movimiento, Sensores e Histogramas|T6 §3]] (preview con histograma).

> [!info] Implementación con convolución
> El paso de predicción es una **convolución 1D** del belief con el kernel `[0.25, 0.5, 0.25]`. Los bordes se manejan con padding "reflective" o explícitamente sumando la masa que "se cae" del borde a la celda extremo.

## Decisiones de diseño / notas de implementación

*(se llena cuando estés resolviendo el TP)*

## Conexiones

- [[Tutorial 6 - Movimiento, Sensores e Histogramas]] — toda la teoría: muestreo, modelo de odometría, filtro discreto.
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — preview de los outputs (scatter de odometría, histograma del filtro).
- [[Filtros Discretos]] — base teórica del bloque 4.
- [[Modelo de Movimiento (Odometría)]] — base teórica del bloque 2.
- [[TP3 - Filtros de Partículas]] — extiende este modelo de movimiento a un filtro de partículas con sensado.

## Fuentes

- `Raw/TPs/Enunciado TP2.pdf`
  - pág. 1 → 1. Muestreo de distribuciones
  - pág. 2 → 2. Modelo de movimiento basado en odometría
  - pág. 3 → 3. Modelo en ROS2 (con screenshot de rviz)
  - pág. 4 → 4. Filtro Discreto
