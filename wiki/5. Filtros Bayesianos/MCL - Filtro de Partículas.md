---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/09-filtro-de-particulas-mcl-4.pdf
  - Raw/Libro/ProbabilisticRobotics.pdf
ultima_actualizacion: 2026-04-28
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

El filtro Bayesiano discreto ([[Filtros Discretos]]) es exacto pero su complejidad crece exponencialmente con la dimensionalidad del espacio. Para un robot móvil 2D con estado $(x, y, \theta)$, una grilla fina puede requerir millones de celdas.

El **Monte Carlo Localization** (MCL) —también llamado **filtro de partículas**— resuelve esta limitación aproximando la distribución continua mediante $N$ muestras ponderadas llamadas **partículas**.

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

> [!info] Por qué low-variance sampling supera al sampling independiente (Thrun et al., §4.2.4)
> El libro destaca tres ventajas del muestreador sistemático sobre tirar $N$ números aleatorios independientes:
> 1. **Cobertura sistemática del espacio de pesos** — recorre todas las particulas en orden, no aleatoriamente. Si todos los pesos son iguales, el resultado es el conjunto original sin pérdida de muestras.
> 2. **Complejidad $O(M)$** — versus $O(M \log M)$ del muestreador independiente con búsqueda binaria. Importa cuando $M$ es grande (10k+ partículas).
> 3. **Menor varianza del estimador** — la dispersión de pesos seleccionados es menor, lo que reduce la pérdida de diversidad por iteración.

> [!warning] No resamplear cuando el estado es estático (Thrun et al., §4.2.4)
> Una propiedad sutil del filtro: si el estado no cambia ($x_t = x_{t-1}$) y el robot no obtiene nuevas mediciones, **resamplear destruye diversidad sin agregar información**. En el límite, después de muchos resamples sin update de medición, todas las partículas convergen a una sola — el filtro "concluye" que el estado está perfectamente determinado, lo cual es falso. Las dos estrategias del libro:
> - **Suspender el resampling** cuando se detecta que el estado es estático (e.g. el robot está parado).
> - **Resamplear menos seguido** — integrar varias mediciones en el peso (multiplicativamente) entre resamples.
> La regla operativa: la varianza de los pesos indica cuándo resamplear. Si los pesos son uniformes (todas las partículas igualmente probables), no resamplees; si están concentrados en pocas partículas, sí.

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

> [!info] MCL es resource-adaptive (Thrun et al., §8.3.2)
> Una propiedad operativa única del MCL: el número de partículas $N$ se puede **cambiar online** según el cómputo disponible. Estrategia clásica: muestrear partículas hasta que llegue el próximo par $(u_t, z_t)$ y entonces actualizar; si la CPU es rápida, $N$ crece y la aproximación mejora; si está saturada, $N$ baja y se pierde precisión pero el filtro sigue corriendo en tiempo real. Ni el filtro de Kalman ni el filtro discreto tienen esta propiedad — el KF tiene complejidad fija por iteración, y el filtro de grilla requiere recompilar la grilla para cambiar resolución. Esta adaptabilidad explica por qué MCL es popular en robots con compute restringido.

## 8. Augmented MCL: recuperarse de kidnapping (Thrun et al., §8.3.3)

El MCL básico **no se recupera del problema del robot secuestrado**. Una vez que las partículas convergen a una pose, no hay forma de migrarlas a otra región del mapa si el robot es trasladado bruscamente. La razón: el resampleo siempre selecciona partículas existentes, nunca crea nuevas en regiones distintas; y el motion model sólo perturba localmente.

**Solución**: inyectar partículas aleatorias durante el resampleo. La pregunta es **cuántas y cuándo**.

### Detección adaptativa de kidnapping

La heurística del libro: si el robot está bien localizado, las nuevas mediciones deberían tener verosimilitud alta bajo el belief actual. Si esa verosimilitud cae bruscamente, probablemente el robot fue secuestrado o el filtro se está perdiendo. Concretamente, se mantienen dos promedios exponencialmente ponderados de la verosimilitud media:

$$w_{\text{avg}} = \frac{1}{M} \sum_{m=1}^{M} w_t^{[m]}$$

$$w_{\text{slow}} \leftarrow w_{\text{slow}} + \alpha_{\text{slow}}\,(w_{\text{avg}} - w_{\text{slow}})$$

$$w_{\text{fast}} \leftarrow w_{\text{fast}} + \alpha_{\text{fast}}\,(w_{\text{avg}} - w_{\text{fast}})$$

con $0 \le \alpha_{\text{slow}} \ll \alpha_{\text{fast}}$ (e.g. 0.05 vs 0.5). El cociente $w_{\text{fast}}/w_{\text{slow}}$ mide cuánto cayó la verosimilitud reciente respecto al promedio histórico.

### Algoritmo Augmented MCL

Durante el resampleo, cada partícula nueva se elige así:
- Con probabilidad $\max\{0,\, 1 - w_{\text{fast}}/w_{\text{slow}}\}$ → **agregar pose aleatoria** (uniforme sobre el mapa libre, o muestreada desde el modelo de sensor en sí — ver §6.6.4).
- Con la probabilidad complementaria → resamplear normalmente desde los pesos.

Cuando el filtro funciona bien, $w_{\text{fast}} \approx w_{\text{slow}}$ y la fracción de partículas aleatorias es ~0. Cuando el robot es secuestrado, $w_{\text{fast}}$ cae rápido y la fracción aleatoria sube — exactamente la situación donde se necesitan nuevas hipótesis.

> [!info] Smoothing exponencial evita falsos positivos
> El término "slow" en el promedio es importante: un único scan ruidoso o una oclusión transitoria no deberían disparar relocalización. El smoothing exponencial filtra esos eventos cortos y sólo activa el random injection cuando la verosimilitud cae *de manera sostenida*.

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
- `Raw/Libro/ProbabilisticRobotics.pdf`
  - págs. 77–80 → Algoritmo del particle filter (§4.2.1)
  - págs. 84–87 → Properties + low-variance sampling + estrategias contra particle impoverishment (§4.2.4)
  - págs. 200–203 → MCL básico + properties resource-adaptive (§8.3.1–§8.3.2)
  - págs. 204–207 → Augmented MCL: random particle injection (§8.3.3)