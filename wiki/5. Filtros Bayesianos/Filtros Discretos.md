---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/08-filtros_discretos-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[Filtros Bayesianos|← Filtros Bayesianos]] | [[Robotica|← Inicio]]

# Filtros Discretos

> Filtro Bayesiano con representación discreta: partición del espacio de estado en celdas
> finitas, donde la creencia se mantiene como vector de probabilidades.

## Prerequisitos
- [[Regla de Bayes]]
- [[Probabilidad - Repaso|Distribuciones de probabilidad]]
- [[Modelo de Movimiento (Velocidad)]]
- [[Modelo de Sensor]]
- [[Localización]]

## 1. Motivación y problema  *(08-filtros_discretos, págs. 1–2)*

El filtro Bayesiano discreto es la formulación práctica más elemental de la localización probabilística.
Dado un espacio de estado finito (o discretizado), la **creencia** sobre el estado del robot se representa
como un vector de probabilidades —una por cada celda—, que suma 1.

El problema de localización en su forma discreta responde a: *"¿En qué celda del mapa se encuentra el robot?"*,
actualizando la respuesta recursivamente con cada acción y cada observación.

## 2. Representación y supuestos  *(08-filtros_discretos, págs. 3–5)*

La creencia se representa como una **distribución de probabilidad por tramos constante** (*piecewise constant*):

- El espacio de estado se divide en $N$ celdas disjuntas: $\{c_1, c_2, ..., c_N\}$.
- La creencia en el instante $t$ es un vector $\mathbf{b}_t = [b(c_1), b(c_2), ..., b(c_N)]$ tal que $\sum_i b(c_i) = 1$.
- Cada celda es igualmente probable dentro de su intervalo; no se modela incertidumbre intra-celda.

![[piecewise-constant-representation.png]]
*Representación por tramos constante de la creencia.*

Esta representación es exacta cuando el estado es naturalmente discreto (p. ej., un robot que se mueve
entre habitaciones) y aproximada cuando el espacio es continuo pero se discretiza con una resolución adecuada.

## 3. Algoritmo Bayesiano discreto  *(08-filtros_discretos, págs. 6–7)*

El filtro discreto sigue la misma estructura predict/correct del filtro Bayesiano general:

### Predicción (motion model)

$$b'(x_t) = \sum_{x_{t-1}} P(x_t | x_{t-1}, u_t) \, b(x_{t-1})$$

En forma matricial:

$$\mathbf{b}'_t = \mathbf{T}_{u_t} \, \mathbf{b}_{t-1}$$

donde $\mathbf{T}_{u_t}$ es la **matriz de transición** cuyas entradas $T_{ij} = P(x_t = c_i | x_{t-1} = c_j, u_t)$
se derivan del modelo de movimiento.

### Corrección (sensor model)

$$b(x_t) = \eta \, P(z_t | x_t) \, b'(x_t)$$

En forma matricial:

$$\mathbf{b}_t = \eta \, \mathbf{Z}_{z_t} \odot \mathbf{b}'_t$$

donde $\mathbf{Z}_{z_t}$ es un vector diagonal de **verosimilitudes** $P(z_t | c_i)$ y $\odot$ denota producto elemento a elemento.

![[discrete-bayes-algorithm.png]]
*Algoritmo Bayesiano discreto — pasos de predicción y corrección.*

### Normalización

El factor $\eta = 1 / \sum_i b(c_i)$ asegura que el vector resultante sume 1. Este factor es la **marginal**
$P(z_t | u_t, z_{1:t-1})$, es decir, la probabilidad de la observación dadas las acciones y observaciones previas.

## 4. Grid-based localization  *(08-filtros_discretos, págs. 8–10)*

La aplicación más directa es la **localización en cuadrícula**:

![[grid-convolution-kernel.png]]
*El modelo de movimiento como convolución sobre la grilla de creencia.*

![[grid-based-localization.png]]
*Grid-based localization: el robot actualiza su creencia con cada acción y sensor.*

El modelo de movimiento se aplica como una **convolución** sobre la grilla. Si el robot avanza una celda hacia adelante
con ruido, el kernel de convolución distribuye la probabilidad entre la celda destino y sus vecinas.

La corrección sensorial multiplica cada celda por su verosimilitud: si el sensor reporta "pared a la izquierda"
y la celda $(i,j)$ tiene pared a la izquierda, $P(z | c_{i,j})$ es alto; si no tiene pared, es bajo.

## 5. Integración con mapas de ocupación  *(08-filtros_discretos, págs. 11–12)*

La representación discreta se combina naturalmente con **mapas de ocupación** generados por sensores sónicos:

![[sonar-occupancy-grid.png]]
*Mapa de ocupación construido a partir de lecturas sónicas.*

Cada lectura del sonar proyecta una celda libre (donde el haz no encontró obstáculo) y una celda ocupada
(en el punto de impacto). Acumulando lecturas se construye un mapa probabilístico que puede usarse
como base para el modelo de sensor en la localización discreta.

## 6. Resumen y limitaciones  *(08-filtros_discretos, pág. 12)*

![[discrete-filters-summary.png]]
*Resumen del filtro Bayesiano discreto.*

**Ventajas:**
- Exacto para espacios discretos finitos.
- Simple de implementar y comprender.
- Convergencia garantizada si el espacio es observable y las transiciones son ergódicas.

**Limitaciones:**
- La complejidad crece **exponencialmente** con la dimensionalidad del espacio (el *curse of dimensionality*).
- Para un robot 2D con posición $(x, y)$ y orientación $\theta$, una grilla fina requiere miles de celdas.
- No captura correlaciones entre celdas vecinas de forma natural.

Estas limitaciones motivan los **filtros basados en muestras** ([[MCL - Filtro de Partículas]])
y los **filtros Gaussianos** ([[Filtro de Kalman]]).

## Conecta con
- ⬅️ [[Localización]] — el problema que este filtro resuelve
- ⬅️ [[Regla de Bayes]] — fundamento probabilístico
- ➡️ [[MCL - Filtro de Partículas]] — aproximación Monte Carlo para espacios continuos
- ➡️ [[Filtro de Kalman]] — aproximación Gaussiana para espacios continuos

## Fuentes
- `Raw/Diapositivas/Teoricas/08-filtros_discretos-3.pdf` — págs. 1–12
