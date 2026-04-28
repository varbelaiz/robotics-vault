---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 7_ Filtro de Partículas.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# Tutorial 7 — Filtro de Partículas

> Aterriza el [[MCL - Filtro de Partículas]] en práctica: filtro **no paramétrico** que representa el belief con un set de muestras (partículas), las propaga con el [[Modelo de Movimiento (Odometría)|modelo de movimiento]], las pondera con la [[Modelo de Sensor|verosimilitud]] de la observación, y las **resamplea** con Stochastic Universal Sampling (SUS) para concentrar densidad en zonas de alta probabilidad. Es el algoritmo de localización del [[TP3 - Filtros de Partículas|TP3]].

## Objetivo

Que cada estudiante pueda:

1. Inicializar un set de $M$ partículas representando el belief inicial.
2. Implementar la **predicción** muestreando del modelo de odometría para cada partícula.
3. Implementar la **actualización** ponderando cada partícula por $w^{[m]} = p(z_t \mid x_t^{[m]})$.
4. Implementar el **resampleo** vía SUS para evitar la depleción de partículas.

## Conceptos que se aplican
- [[Filtro de Bayes]] — base teórica.
- [[MCL - Filtro de Partículas]] — la teoría central.
- [[Muestreo por Importancia]] — fundamento del weighting.
- [[Modelo de Movimiento (Odometría)]] — el `sample_motion_model_odometry` para la predicción.
- [[Modelo de Sensor]] (beam, likelihood field, o landmarks) — para los pesos.

## Estructura del tutorial

| Slides | Sección |
|---|---|
| 1–8 | Recap del filtro de Bayes + likelihood field |
| 9–28 | Features y data association (panorama, no profundizado) |
| 29–30 | Filtro de partículas — definición conceptual |
| 31–36 | Predicción |
| 37–51 | Actualización (con ejemplo de landmark) |
| 52–54 | Normalización |
| 55–73 | Remuestreo + Stochastic Universal Sampling paso a paso |
| 74 | Vista global del algoritmo |

## 1. Filtro de Partículas — Definición

![[Tutorial 7 - filtro particulas algoritmo.png]]
*Pseudocódigo + visualización: el set de partículas $\bar{\mathcal{X}}_t$ se forma muestreando $x_t^{[m]} \sim p(x_t \mid u_t, x_{t-1}^{[m]})$, ponderando $w_t^{[m]} = p(z_t \mid x_t^{[m]})$ y resampleando con probabilidad proporcional al peso. La nube roja en el mapa es la representación del belief, slide 29.*

![[Tutorial 7 - filtro particulas definicion.png]]
*Cuatro propiedades clave: filtro (procesa lecturas crudas), no paramétrico (no asume distribución), partículas (escala con la complejidad), resource-adaptive (puede bajar partículas con menos recursos), slide 30.*

> [!info] No paramétrico vs. paramétrico
> Kalman es **paramétrico**: asume gaussiana, todo lo representa con $(\mu, \Sigma)$. El filtro de partículas no asume forma: si la posterior es multimodal, las partículas la siguen.

## 2. Las tres fases

![[Tutorial 7 - prediction update resampling.png]]
*El ciclo del filtro: prediction (mover cada partícula con el modelo de movimiento) → update (ponderar con la observación) → resampling (regenerar el set proporcional a los pesos), slide 31.*

```
Algorithm Particle_filter(X_{t-1}, u_t, z_t):
    X̄_t = X_t = ∅
    for m = 1 to M do
        sample x_t^[m] ~ p(x_t | u_t, x_{t-1}^[m])         # PREDICCIÓN
        w_t^[m] = p(z_t | x_t^[m])                          # ACTUALIZACIÓN
        X̄_t = X̄_t ∪ ⟨x_t^[m], w_t^[m]⟩
    endfor
    for m = 1 to M do
        draw i with probability ∝ w^[i]                     # RESAMPLEO
        add x_t^[i] to X_t
    endfor
    return X_t
```

## 3. Predicción

![[Tutorial 7 - prediccion detalle deltas.png]]
*Cada partícula $x_t^{[m]}$ se obtiene aplicando `sample_motion_model_odometry` desde la partícula previa $x_{t-1}^{[m]}$ con el control $u_t$ (los deltas de odometría). El ruido del modelo dispersa las partículas, slide 35.*

Esto es exactamente lo del [[Tutorial 6 - Movimiento, Sensores e Histogramas|Tutorial 6 §1]] — la diferencia es que ahora se aplica **una vez por partícula**, no para obtener un único $x_t$ probabilístico. El conjunto de partículas tras la predicción **es** la distribución $\bar{Bel}(x_t)$.

> [!info] El mismo ejemplo de muestreo del TP2
> El scatter plot de "5000 muestras alrededor del nuevo estado" del TP2 es exactamente lo que pasa en el filtro de partículas — sólo que con $M$ partículas (típicamente 100–1000), no 5000.

## 4. Actualización: ponderar con la observación

Para cada partícula $x_t^{[m]}$, su peso es la verosimilitud de la observación dada esa pose:

$$w_t^{[m]} = p(z_t \mid x_t^{[m]})$$

El modelo $p(z \mid x)$ puede ser cualquiera de los del [[Modelo de Sensor|módulo 3]] — beam-based, likelihood field, o landmarks. El tutorial trabaja el caso **landmarks**:

### Ejemplo: una partícula, un landmark

![[Tutorial 7 - actualizacion landmark gaussiana.png]]
*El robot mide distancia $z$ al landmark; la partícula "espera" distancia $\check{z}$ desde su pose. El peso es $p(z - \check{z})$ evaluado en una gaussiana, slide 42.*

### Ejemplo: dos partículas

![[Tutorial 7 - actualizacion dos particulas angulo.png]]
*Dos partículas con la misma distancia esperada $\check{z}$ pero ángulos esperados distintos $\theta_1, \theta_2$. El peso combina ambas mediciones (distancia + ángulo): $w \propto p(z - \check{z}) \cdot p(\theta - \theta_i)$, slide 45.*

> [!info] Por qué multiplicar
> Asumimos independencia condicional de las mediciones (distancia y ángulo) dado el estado: $p(z, \theta \mid x) = p(z \mid x)\,p(\theta \mid x)$. Esto justifica multiplicar las dos likelihoods.

### Múltiples landmarks

![[Tutorial 7 - actualizacion dos landmarks.png]]
*Con dos landmarks, el peso de cada partícula es el producto de las verosimilitudes para cada landmark: $P(\text{Particula}) = P(L_1 \mid \text{Particula}) \cdot P(L_2 \mid \text{Particula})$. La partícula "correcta" tiene mucho mayor peso, slide 50.*

## 5. Normalización

![[Tutorial 7 - normalizacion pesos.png]]
*Tras pesar todas las partículas, sus pesos no suman 1. Se normalizan dividiendo por la suma total: $w^{[m]} \leftarrow w^{[m]} / \sum_j w^{[j]}$. La nube de partículas representa el belief actualizado, slide 54.*

> [!info] La constante $\eta$ del filtro de Bayes
> En las fórmulas analíticas $\eta = 1/p(z_t)$. En el filtro de partículas $\eta$ se calcula implícitamente como $1/\sum_j w^{[j]}$ — no hay que evaluar la integral $p(z_t)$.

## 6. Remuestreo

Sin remuestreo, después de varios ciclos la mayoría de las partículas tienen peso despreciable y unas pocas concentran toda la probabilidad — **partícula degeneration**. La solución es **resamplear**: sortear $M$ partículas con probabilidad proporcional a sus pesos.

### Estrategia 1: thresholding simple

> Sacar todos los pesos < 5%, replicar el resto.

Funciona pero converge lento y es muy exigente con el threshold.

### Estrategia 2: Stochastic Universal Sampling (SUS)

Algoritmo "genético" que distribuye los samples uniformemente sobre la CDF de pesos:

![[Tutorial 7 - SUS algoritmo.png]]
*Algoritmo `systematic_resampling(S, n)`: construye CDF de pesos, elige seed $u_1 \sim U[0, 1/n]$, y avanza con incremento $1/n$. Cada vez que el threshold $u_j$ supera el bin acumulado $c_i$, avanza al siguiente bin; si no, copia la partícula $i$ al output, slide 56.*

```python
# Stochastic Universal Sampling — concepto
def sus(weights, M):
    # CDF de pesos (asumiendo normalizado)
    cdf = cumsum(weights)
    seed = uniform(0, 1/M)
    new_particles = []
    i = 0
    for j in range(M):
        u = seed + j * (1/M)
        while u > cdf[i]:
            i += 1
        new_particles.append(particles[i])
    return new_particles
```

### Ejemplo paso a paso

Con 10 partículas de pesos $[0.2, 0.1, 0.15, 0.05, 0.05, 0.15, 0.05, 0.1, 0.05, 0.1]$:

- Seed $u_1 \in [0, 0.1)$, digamos $0.075$.
- Threshold $u_j = 0.075 + j \cdot 0.1$, $j = 0, \dots, 9$.
- Para cada $u_j$, copiar la partícula cuya CDF acumulada supere el threshold por primera vez.

![[Tutorial 7 - SUS resultado final.png]]
*Resultado: el set resampleado es $\{p_1, p_1, p_2, p_3, p_4, p_6, p_6, p_8, p_9, p_{10}\}$. Las partículas con mayor peso ($p_1$ con 0.2, $p_6$ con 0.15) se replican; $p_5$ y $p_7$ con peso 0.05 quedan fuera, slide 73.*

> [!warning] Las partículas duplicadas no son idénticas a t+1
> Aunque dos partículas iniciales sean copias, en el siguiente paso de predicción se les muestrea ruido independiente del modelo de movimiento. Eso es lo que mantiene la diversidad del set: el resampleo concentra densidad, el ruido de movimiento la dispersa de nuevo.

## 7. Features y data association (preview)

El tutorial le dedica un bloque (slides 9–28) a **landmarks/features** — qué son, cómo se extraen del lidar, y cómo se asocia una observación al landmark correcto:

![[Tutorial 7 - features panorama.png]]
*Panorama de marcadores: balizas activas (radio, GPS), marcadores pasivos (visual, retro-reflexivo), método más común triangulación. El sensor provee distancia y/o orientación, slide 9.*

![[Tutorial 7 - features extraccion lidar.png]]
*Extracción del lidar: ajuste de líneas o esquinas a los puntos del scan. Se usa LSQ, RANSAC u otras técnicas. Importante: extraer las **mismas** features del mapa para poder asociar, slide 18.*

![[Tutorial 7 - nearest neighbour.png]]
*Data association con Nearest Neighbour: cada feature observada se asocia al landmark del mapa más cercano (en distancia + ángulo). Las líneas verde y roja muestran las asociaciones predicha vs. observada, slide 25.*

Detalle teórico en [[Modelo de Detección de Landmarks]] (M4). Para el TP3 con Turtlebot, el modelo de sensor es **likelihood field** sobre el lidar — no se usan features explícitas.

## 8. Recap visual

![[Tutorial 7 - recap modelo odometria.png]]
*Recap del modelo de odometría: $P(x_t \mid u_t, x_{t-1})$ aterrizado como muestreo desde la última pose, slide 3.*

## Conexiones
- [[Tutorial 6 - Movimiento, Sensores e Histogramas]] — tutorial anterior (los modelos de movimiento y sensor que aterriza este tutorial).
- [[Tutorial 8 - KF, EKF, UKF]] — siguiente: filtros gaussianos paramétricos.
- [[MCL - Filtro de Partículas]] — teoría central.
- [[Muestreo por Importancia]] — fundamento de la ponderación.
- [[Modelo de Detección de Landmarks]] — un modelo de sensor posible.
- [[TP3 - Filtros de Partículas]] — implementación completa en ROS2.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 7_ Filtro de Partículas.pdf`
  - slides 2–8 → Recap (filtro de Bayes + likelihood field)
  - slides 9–28 → 7. Features y data association (preview)
  - slides 29–30 → 1. Filtro de Partículas — Definición
  - slide 31 → 2. Las tres fases
  - slides 32–36 → 3. Predicción
  - slides 37–51 → 4. Actualización
  - slides 52–54 → 5. Normalización
  - slides 55–73 → 6. Remuestreo + SUS
