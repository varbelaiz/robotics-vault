---
modulo: 8. TPs
estado: completo
fuentes:
  - Raw/TPs/Enunciado TP3.pdf
ultima_actualizacion: 2026-04-27
---

> [[TPs|← TPs]] | [[Robotica|← Inicio]]

# TP3 — Filtros de Partículas

> Implementación completa de [[MCL - Filtro de Partículas|MCL]] en ROS2 sobre un Turtlebot simulado: (1) lanzar la stack de nodos, (2) precomputar el **campo de verosimilitud** desde el `OccupancyGrid` del mapa, (3) aplicar el **modelo de odometría con ruido** a cada partícula, (4) **ponderar y resamplear** con el likelihood field para localizar.

## Objetivo

Que el estudiante construya un filtro MCL funcional — al mover el robot con teleop, las partículas deben **converger** a la pose real y la trayectoria estimada (rojo en RVIZ) debe seguir a la real (verde).

## Conceptos que necesitás

- [[MCL - Filtro de Partículas]] — la teoría central.
- [[Filtro de Bayes]] — base.
- [[Muestreo por Importancia]] — fundamento del weighting.
- [[Modelo de Movimiento (Odometría)]] — el `sample_motion_model_odometry` para la predicción de cada partícula.
- [[Modelo de Campo de Verosimilitud]] — el modelo de sensor del TP.
- [[Mapas de Ocupación]] — el `OccupancyGrid` que llega por topic; valores 0 (libre), 100 (ocupado), -1 (desconocido).
- [[ROS2 - TF2]] — para componer scan del lidar al frame del mundo.
- [[ROS2 - Mensajes]] — `OccupancyGrid`, `LaserScan`, `Pose`, `PoseArray`.
- [[Tutorial 7 - Filtro de Partículas]] — algoritmo paso a paso.
- [[Tutorial 8 - KF, EKF, UKF]] — el truco numérico de log-likelihoods aplica acá.

## Pasos del enunciado

### 1. Proceso de lanzamiento

Tres terminales (no olvidar `colcon build` + `source install/setup.bash`):

```bash
# Terminal 1: simulador con la habitación custom
ros2 launch turtlebot3_custom_simulation custom_room.launch.py

# Terminal 2: nodo de partículas
ros2 launch tp3 launch_my_particles.launch.py

# Terminal 3: teleop
ros2 run turtlebot3_teleop teleop_keyboard
```

![[TP3 - rviz mapa sin likelihood.png]]
*Estado inicial: mapa visible, partículas estáticas (no se mueven), línea verde = camino real, línea azul = odometría, falta la línea roja del filtro de partículas.*

### 2. Campo de Verosimilitud

Implementar `map_callback` en `likelihood.py`. El nodo se suscribe al topic del mapa y debe **publicar el campo de verosimilitud** como un `OccupancyGrid` con valores 0–100.

**Detalles del input**:
- `msg.data` es un array 1D ordenado: empieza en la **esquina inferior izquierda**, avanza por filas (izquierda → derecha), después fila superior, etc.
- Valores: `0` = libre, `100` = ocupado, `-1` = desconocido.

**Lo que hay que computar**: para cada celda libre, calcular su distancia al obstáculo más cercano y aplicar una gaussiana — exactamente el procedimiento de [[Modelo de Campo de Verosimilitud]] (M4).

![[TP3 - rviz likelihood field.png]]
*Después de implementar `map_callback`: el likelihood field se renderiza como halos suaves alrededor de cada obstáculo (escala de grises).*

> [!info] Algoritmo eficiente: Distance Transform
> En vez de iterar todas las celdas para cada celda libre ($O(n^2)$), usar `scipy.ndimage.distance_transform_edt` para obtener la distancia al obstáculo más cercano en $O(n)$. Después aplicar la gaussiana es un map operation.

### 3. Movimiento de Partículas

El paquete provee la clase `particle` con campos `position`, `orientation`, `weight` y un método para mover por odometría. Tareas:

1. **Implementar la propagación**: para cada partícula, llamar a su método de odometría con los deltas $(δ_{r1}, δ_t, δ_{r2})$ y los $\alpha$ de ruido — exactamente el `sample_motion_model_odometry` del [[TP2 - Modelos Probabilísticos y Filtros Discretos|TP2]].
2. **Implementar `get_selected_state`**: devuelve el estado estimado del robot $(x, y, \theta)$ a partir del set de partículas. Métodos posibles:
   - **Promedio ponderado**: $\hat{x} = \sum_i w_i \cdot x_i$.
   - **MAP**: la partícula con peso máximo.
   - **Cluster**: si las partículas son multimodales, elegir el cluster más denso.

> [!info] Para $\theta$ no usar promedio aritmético
> El promedio de ángulos es un problema circular ($\theta = 0$ y $\theta = 2\pi$ son iguales). Promediar como vectores: $\theta = \text{atan2}(\sum w_i \sin\theta_i, \sum w_i \cos\theta_i)$.

Por default 1000 partículas. Si va muy lento o no converge:

```bash
ros2 launch custom_code launch_my_particles.launch.py num_particles:=500
```

![[TP3 - rviz particulas dispersas.png]]
*Con sólo movimiento (sin sensado todavía), las partículas se dispersan acumulando ruido — sin un mecanismo de corrección, divergen del estado real.*

### 4. Sensado y Resampleo

Implementar `update_particles(data, map_data, grid)`:

| Parámetro | Tipo | Descripción |
|---|---|---|
| `data` | `LaserScan` | scan crudo del lidar; usar `scan_reference` para convertir a posiciones globales (igual que TP1) |
| `map_data` | `OccupancyGrid` | mensaje crudo del likelihood field |
| `grid` | `np.ndarray` | el likelihood field como matriz numpy |

**Indexado del grid** (importante):
- `grid[y, x]` — primero fila (eje Y), después columna (eje X).
- `(0, 0)` = esquina inferior izquierda en coordenadas ROS.

**Pasos**:

1. Para cada partícula: convertir el scan a posiciones globales como si la pose verdadera fuera la de la partícula. Para cada haz, lookup en `grid[y, x]` → multiplicar likelihoods.
2. Asignar el peso resultante a cada partícula.
3. **Resamplear**: sortear $M$ partículas con probabilidad proporcional al peso. **Crítico**: hacer `deepcopy` para que las partículas resampleadas sean objetos Python distintos (si no, el ruido de movimiento se aplica al mismo objeto múltiples veces).
4. Guardar el set resampleado en `self.particles`.

> [!warning] Underflow numérico con muchos haces
> Con 360 haces y likelihoods $\sim 10^{-3}$, el producto colapsa a 0. **Usar log-likelihoods + max-shift** (ver [[Tutorial 8 - KF, EKF, UKF|Tutorial 8 §1]]).

> [!info] Resamplear con SUS
> El [[Tutorial 7 - Filtro de Partículas|Tutorial 7 §6]] desarrolla Stochastic Universal Sampling — implementación más estable que `random.choices` con replacement.

**Resultado esperado**: al mover el robot con teleop, las partículas convergen a la pose real, y el camino rojo (estimación) sigue al verde (real).

## Decisiones de diseño / notas de implementación

*(se llena cuando estés resolviendo el TP)*

Cosas que típicamente hay que decidir:

- **Frecuencia del resampleo**: ¿cada update? ¿cada N updates? ¿cuando la varianza efectiva $N_{eff}$ baja de un threshold?
- **Cantidad de partículas**: 1000 default, pero 500 alcanza si el algoritmo es eficiente.
- **Política de inicialización**: distribución uniforme sobre todo el mapa, o concentrada cerca de la pose conocida.
- **`get_selected_state`**: el método que elijas afecta cómo se ve el camino rojo en RVIZ.

## Conexiones

- [[Tutorial 7 - Filtro de Partículas]] — algoritmo MCL paso a paso (predicción + actualización + SUS).
- [[Tutorial 8 - KF, EKF, UKF]] — log-likelihoods + max-shift; integración paralela en ROS2 (slide 79).
- [[Tutorial 6 - Movimiento, Sensores e Histogramas]] — modelo de odometría + likelihood field.
- [[TP1 - Transformaciones, Locomoción y Sensado]] — la función `scan_reference` es el mismo cálculo que el bloque 2 del TP1.
- [[TP2 - Modelos Probabilísticos y Filtros Discretos]] — el modelo de odometría con ruido se reusa.
- [[MCL - Filtro de Partículas]] / [[Modelo de Campo de Verosimilitud]] / [[Muestreo por Importancia]] — teoría central.

## Fuentes

- `Raw/TPs/Enunciado TP3.pdf`
  - pág. 1 → 1. Proceso de lanzamiento de nodos
  - pág. 2 → 2. Campo de Verosimilitud
  - págs. 3–4 → 3. Movimiento de Partículas
  - pág. 5 → 4. Sensado y Resampleo
