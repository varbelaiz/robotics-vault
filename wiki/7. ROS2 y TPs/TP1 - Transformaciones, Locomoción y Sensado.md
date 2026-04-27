---
modulo: 7. ROS2 y TPs
estado: completo
fuentes:
  - Raw/TPs/Enunciado TP1.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2 y TPs|← ROS2 y TPs]] | [[Robotica|← Inicio]]

# TP1 — Transformaciones, Locomoción y Sensado

> Tres bloques: (1) álgebra de **transformaciones homogéneas 2D** entre ternas robot/mundo/obstáculo, (2) **lectura y graficado de datos reales** del robot (odometría con cuaterniones + scan del lidar), (3) **cinemática directa** del accionamiento diferencial vía la función `diffdrive`.

## Objetivo

Que el estudiante pueda:

1. Componer e invertir transformaciones homogéneas 2D entre frames del robot, del mundo y de un obstáculo.
2. Leer datos reales (`odom.csv`, `scan.pkl`), interpretar la pose (cuaternión → yaw), y proyectar el lidar al frame global usando la transformación robot→lidar.
3. Implementar la función `diffdrive(x, y, theta, v_l, v_r, t, l)` y simular una secuencia de comandos.

## Conceptos que necesitás

- [[Transformaciones Homogéneas]] — matriz $T$, composición e inversa.
- [[Rotaciones]] — la submatriz $R(\theta)$.
- [[Cuaterniones]] — `/odom` publica orientación como cuaternión; hay que convertir.
- [[Cinemática del Robot Diferencial]] — `diffdrive` es exactamente la cinemática directa de la sección 2 del módulo 2.
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — formato `LaserScan` (ranges, intensities, angle_min/max/increment, range_min/max).
- [[ROS2 - TF2]] — la matriz `robotTlidar` es exactamente lo que TF2 publicaría como transformación estática.
- [[ROS2 - Mensajes]] — composición de `nav_msgs/Odometry` con `Pose` que contiene `Quaternion`.

## Pasos del enunciado

### 1. Transformaciones 2D y matrices afines

La pose $\mathbf{x} = (x, y, \theta)^T$ se representa con la matriz homogénea:

$$\mathbf{T} = \begin{pmatrix} \mathbf{R}(\theta) & \mathbf{t} \\ \mathbf{0} & 1 \end{pmatrix}, \quad \mathbf{R}(\theta) = \begin{pmatrix} \cos\theta & -\sin\theta \\ \sin\theta & \cos\theta \end{pmatrix}, \quad \mathbf{t} = \begin{pmatrix} x \\ y \end{pmatrix}$$

Resolver:

1. Robot en pose $\mathbf{x}_1$ ve un obstáculo $p$ en su terna local con coordenadas $(p_x, p_y)$. Usar $\mathbf{T}_1$ para expresar $p$ en la **terna global**.
2. Dado un obstáculo en la terna global, ¿cómo lo expresamos en la terna del robot? *(invertir $\mathbf{T}$)*.
3. El robot pasa de $\mathbf{x}_1$ a $\mathbf{x}_2$. Encontrar la matriz $\mathbf{T}_{12}$ que representa la nueva pose **respecto a $\mathbf{x}_1$**.
4. Estando en $\mathbf{x}_2$, ¿dónde está el obstáculo $p$ respecto a la nueva terna local?

> [!info] La inversa de una transformación homogénea
> $\mathbf{T}^{-1} = \begin{pmatrix} \mathbf{R}^T & -\mathbf{R}^T \mathbf{t} \\ \mathbf{0} & 1 \end{pmatrix}$. No hace falta inversa numérica: $\mathbf{R}$ es ortogonal, así que $\mathbf{R}^{-1} = \mathbf{R}^T$.

### 2. Sensado con datos reales

Dos archivos:

- **`odom.csv`** — pose del robot real con `(x, y, z, qx, qy, qz, qw)`. La rotación viene como **cuaternión** (ver [[Cuaterniones]] §4 — workflow conversión yaw).
- **`scan.pkl`** — `LaserScan` con `angle_min, angle_max, angle_increment, range_min, range_max, ranges, intensities`. **Filtrar**: descartar mediciones fuera de `[range_min, range_max]` y rayos con `intensity ≤ 0`.

La transformación lidar → robot está fija (sensor atornillado):

$$^{\text{robot}}T_{\text{lidar}} = \begin{pmatrix} 0 & -1 & 0 & -0.04 \\ 1 & 0 & 0 & 0 \\ 0 & 0 & 1 & 0.193 \\ 0 & 0 & 0 & 1 \end{pmatrix}$$

> [!info] Aunque la matriz sea 3D, los gráficos pueden ser 2D
> El lidar barre en el plano $xy$; el offset en $z$ (0.193) sólo importa si proyectás a 3D. Para el TP, ignorá $z$.

Ejercicios:

1. Graficar las mediciones en la **terna del LIDAR** (polar o cartesiano).
2. Interpretar el entorno (¿hay paredes? ¿obstáculos?).
3. Usar transformaciones homogéneas para calcular y graficar:
   - Pose del robot en la terna global (de odometría).
   - Pose del LIDAR en la terna global (componer $T_{\text{robot}} \cdot {}^{\text{robot}}T_{\text{lidar}}$).
   - Mediciones en la terna global.

### 3. Accionamiento diferencial

Implementar `diffdrive(x, y, theta, v_l, v_r, t, l) -> x_n, y_n, theta_n` con la **cinemática directa** del robot diferencial. Detalle teórico en [[Cinemática del Robot Diferencial]].

Probar con $\mathbf{x}_0 = (0, 0, \pi/4)$, $l = 0.5$ m, ejecutando la secuencia:

| # | $v_l$ (m/s) | $v_r$ (m/s) | $t$ (s) |
|---|---|---|---|
| 1 | 0.1 | 0.5 | 2 |
| 2 | 0.5 | 0.1 | 2 |
| 3 | 0.2 | 0.2 | 2 |
| 4 | 1.0 | 0.0 | 4 |
| 5 | 0.4 | 0.4 | 2 |
| 6 | 0.2 | -0.2 | 2 |
| 7 | 0.5 | 0.5 | 2 |

Graficar la trayectoria resultante.

> [!warning] Casos especiales
> - $v_l = v_r$ → línea recta (sin rotación), no usar la fórmula con ICC porque divide por cero.
> - $v_l = -v_r$ → rotación pura sobre el centro del robot.

## Decisiones de diseño / notas de implementación

*(se llena cuando estés resolviendo el TP)*

## Conexiones

- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — preview del TP1 con resultados esperados (LIDAR scan, point cloud transformado, robot path).
- [[Tutorial 3 - Interactuando con Gazebo]] — el código del `Recorder` (subscriber + atexit) que produce `odom.csv`.
- [[Cinemática del Robot Diferencial]] — la teoría detrás de `diffdrive`.
- [[Cuaterniones]] — para convertir las orientaciones del CSV.
- [[ROS2 - TF2]] — la matriz `robotTlidar` es una static transformation.

## Fuentes

- `Raw/TPs/Enunciado TP1.pdf`
  - pág. 1 → 1. Transformaciones 2D y matrices afines
  - págs. 2–3 → 2. Sensado (con `robotTlidar`)
  - pág. 3 → 3. Accionamiento diferencial
