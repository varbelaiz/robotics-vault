---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/09-filtro-de-particulas-mcl-4.pdf
ultima_actualizacion: 2026-04-26
---

> [[Filtros Bayesianos|← Filtros Bayesianos]] | [[Robotica|← Inicio]]

# MCL - Filtro de Partículas

> Algoritmo de localización probabilística que aproxima la distribución de creencia
> mediante un conjunto ponderado de partículas (muestras hipotéticas del estado del robot).

## Prerequisitos
- [[Filtros Discretos]]
- [[Muestreo por Importancia]]
- [[Modelo de Movimiento (Velocidad)]]
- [[Modelo de Sensor]]
- [[Localización]]

## 1. Motivación

El filtro Bayesiano discreto ([[Filtros Discretos]]) es exacto pero su complejidad crece exponencialmente
con la dimensionalidad del espacio. Para un robot móvil 2D con estado $(x, y, \theta)$,
una grilla fina puede requerir millones de celdas.

El **Monte Carlo Localization** (MCL) —también llamado **filtro de partículas**— resuelve esta limitación
aproximando la distribución continua mediante $N$ muestras ponderadas llamadas **partículas**.

![[mcl-context.png]]
*El problema de localización y por qué se necesita MCL.*

Cada partícula representa una hipótesis sobre el estado del robot: "el robot *podría* estar aquí".
Las partículas cercanas a la posición real acumulan mayor peso; las lejanas, menor peso.

## 2. Representación de la creencia

La creencia se aproxima como una mezcla discreta de deltas de Dirac:

$$b(x_t) \approx \sum_{i=1}^N w^{(i)} \, \delta(x_t - x_t^{(i)})$$

donde:
- $x_t^{(i)} = (x^{(i)}, y^{(i)}, \theta^{(i)})$ es la **posición hipotética** de la partícula $i$.
- $w^{(i)}$ es el **peso** de la partícula $i$, con $\sum_i w^{(i)} = 1$.

![[particle-approximation.png]]
*Aproximación de la creencia mediante partículas ponderadas.*

La **media de la creencia** se estima como:

$$\hat{x}_t = \sum_{i=1}^N w^{(i)} \, x_t^{(i)}$$

La **varianza** como:

$$\text{Var}(x_t) = \sum_{i=1}^N w^{(i)} \, (x_t^{(i)} - \hat{x}_t)^2$$

## 3. Algoritmo

![[particle-filter-algorithm-steps.png]]
*Pasos del algoritmo de filtro de partículas.*

![[particle-filter-pseudocode.png]]
*Pseudocódigo del filtro de partículas.*

### Paso 0 — Inicialización
Distribuir $N$ partículas uniformemente sobre todo el mapa (ignorancia total).

### Paso 1 — Predicción (motion model)
Para cada partícula $i$, actualizar su posición aplicando el modelo de movimiento con el control $u_t$,
sumando ruido aleatorio:

$$x_t^{(i)} = f(x_{t-1}^{(i)}, u_t, \varepsilon_t^{(i)})$$

donde $\varepsilon_t^{(i)}$ es ruido gaussiano muestreado aleatoriamente.

### Paso 2 — Corrección (sensor model)
Para cada partícula, calcular el peso basado en la verosimilitud de la observación actual:

$$w^{(i)} \propto w^{(i)} \cdot P(z_t | x_t^{(i)})$$

El modelo de sensor $P(z_t | x_t^{(i)})$ se evalúa simulando qué mediría el robot si estuviera en $x_t^{(i)}$
y comparándolo con la lectura real $z_t$.

### Paso 3 — Resampling
Seleccionar $N$ nuevas partículas de la población actual, con probabilidad proporcional al peso.
Las partículas con alto peso se duplican; las de peso bajo desaparecen.

## 4. Resampling

![[systematic-resampling.png]]
*Algoritmo de resampling sistemático.*

El resampling es el paso que concentra las partículas alrededor de las regiones de alta probabilidad.
Sin resampling, la mayoría de las partículas tendría peso cercano a cero (*weight degeneration*).

### Problema: partición degeneración
Después de pocas iteraciones, una sola partícula puede dominar todos los pesos.
El resampling soluciona esto al eliminar partículas de bajo peso y duplicar las de alto peso.

![[resampling-necessity.png]]
*Necesidad del resampling: sin él, las partículas se dispersan sin converger.*

### Algoritmo de resampling sistemático
1. Generar un número aleatorio $r \in [0, 1/N]$.
2. Para $k = 0, ..., N-1$:
   - Calcular $u_k = r + k/N$.
   - Seleccionar la partícula $i$ cuyo acumulador de peso cubre $u_k$.

Este método es más eficiente que el resampling discreto simple porque solo necesita un número aleatorio.

## 5. Modelo de movimiento

El modelo de movimiento se compone en dos pasos: rotación y traslación, siguiendo el modelo del
odómetro del robot.

![[motion-model-decomposition.png]]
*Descomposición del modelo de movimiento en rotación + traslación.*

### Rotación 1 (girar sobre sí mismo)
$$\theta_1 = \theta_{t-1} + \Delta\theta_1 + \varepsilon_1$$

### Traslación (avanzar)
$$x_{temp} = x_{t-1} + \frac{d}{\Delta\theta_2 + \varepsilon_2} (\sin(\theta_1 + \Delta\theta_2 + \varepsilon_2) - \sin(\theta_1))$$
$$y_{temp} = y_{t-1} - \frac{d}{\Delta\theta_2 + \varepsilon_2} (\cos(\theta_1 + \Delta\theta_2 + \varepsilon_2) - \cos(\theta_1))$$

### Rotación 2 (ajuste final de orientación)
$$\theta_t = \theta_1 + \Delta\theta_2 + \varepsilon_2$$

donde $\varepsilon_1, \varepsilon_2$ son ruido gaussiano proporcional a los comandos de movimiento (modelado con coeficientes $a_1, a_2, ...$).

![[motion-model-noise.png]]
*Ruido añadido al modelo de movimiento.*

## 6. Ejemplo de localización práctica

![[mobile-robot-localization.png]]
*Localización del robot móvil mediante MCL.*

El proceso completo en un escenario real:
1. El robot parte con partículas distribuidas uniformemente (**kidnapped robot problem**).
2. A medida que se mueve y toma lecturas del sensor, las partículas se reponderan.
3. Después de ~10 mediciones sónicas, la distribución se concentra en la posición correcta.

![[after-10-sonar-measurements.png]]
*Estado de las partículas después de 10 mediciones del sónico.*

![[estimated-trajectory.png]]
*Trayectoria estimada por MCL vs trayectoria real.*

## 7. Ventajas y desventajas

**Ventajas:**
- Funciona en espacios **multimodales** (puede representar múltiples hipótesis simultáneamente).
- Maneja espacios de estado continuos y de alta dimensión.
- Resolución **adaptativa**: concentra partículas donde la probabilidad es alta.
- Soluciona el *kidnapped robot problem* naturalmente.

**Desventajas:**
- No es exacto; es una aproximación Monte Carlo ($O(1/\sqrt{N})$).
- Requiere sintonizar: número de partículas, modelo de sensor, parámetros de ruido.
- **Particle depletion**: si el resampling elimina todas las partículas correctas por mala suerte, el filtro se pierde.
- Coste computacional proporcional a $N$ (pero lineal, no exponencial).

## Conecta con
- ⬅️ [[Filtros Discretos]] — el filtro exacto que MCL aproxima
- ⬅️ [[Muestreo por Importancia]] — técnica matemática subyacente
- ⬅️ [[Modelo de Movimiento (Velocidad)]] — se usa en el paso de predicción
- ⬅️ [[Modelo de Sensor]] — se usa en el paso de corrección
- ➡️ [[Filtro de Kalman]] — alternativa Gaussiana para espacios unimodales

## Fuentes
- `Raw/Diapositivas/Teoricas/09-filtro-de-particulas-mcl-4.pdf`
  - págs. 1–2 → 1. Motivación
  - págs. 3–5 → 2. Representación de la creencia
  - págs. 21–22 → 3. Algoritmo
  - págs. 24–26 → 4. Resampling
  - págs. 25–26 → 5. Modelo de movimiento
  - págs. 27+ → 6. Ejemplo de localización práctica