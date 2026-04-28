---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/10-filtro_de_kalman-3.pdf
  - Raw/Libro/ProbabilisticRobotics.pdf
ultima_actualizacion: 2026-04-28
---

> [[Filtros Bayesianos|← Filtros Bayesianos]] | [[Robotica|← Inicio]]

# Filtro de Kalman

> Estimador óptimo recursivo para sistemas lineales con ruido gaussiano.
> Mantiene la creencia como una Gaussiana multivariada (mu, Sigma).

## Prerequisitos
- [[Regla de Bayes]]
- [[Gaussiana Multivariada]]
- [[Algebra Lineal|Matrices y operaciones]]
- [[Filtros Discretos]] — la versión discreta de la misma idea

## 1. Introducción

El filtro de Kalman es el **estimador óptimo** (mínimo error cuadrático medio) para sistemas
que cumplen dos condiciones:
1. El modelo de dinámica y el modelo de observación son **lineales**.
2. Los ruidos son **Gaussianos**.

![[kalman-intro.png]]
*Introducción al filtro de Kalman.*

Bajo estos supuestos, el filtro mantiene la creencia como una única Gaussiana multivariada,
representada compactamente por solo dos parámetros: la media y la covarianza.

> [!info] Los 3 supuestos lineal-gaussianos formales (Thrun et al., §3.2.1)
> El libro formaliza tres condiciones que **conjuntamente** garantizan que la posterior $bel(x_t)$ sea gaussiana en todo $t$:
> 1. **Transición lineal con ruido gaussiano**: $x_t = A_t x_{t-1} + B_t u_t + \varepsilon_t$ con $\varepsilon_t \sim \mathcal{N}(0, R_t)$.
> 2. **Medición lineal con ruido gaussiano**: $z_t = C_t x_t + \delta_t$ con $\delta_t \sim \mathcal{N}(0, Q_t)$.
> 3. **Belief inicial gaussiano**: $bel(x_0) \sim \mathcal{N}(\mu_0, \Sigma_0)$.
>
> El tercer supuesto es fácil de pasar por alto pero es esencial: sin un prior gaussiano, la posterior nunca lo sería. La prueba de que estos tres bastan vive en la derivación matemática de §3.2.4 — una verificación inductiva con el truco del *inversion lemma* aplicado a las gaussianas que se multiplican.

## 2. Modelo del sistema

### Modelo de transición (dinámica)

$$x_t = A_t x_{t-1} + B_t u_t + \varepsilon_t$$

donde:
- $x_t$ es el **estado** en el tiempo $t$ (p. ej., posición + velocidad).
- $u_t$ es el **control** (acción del robot).
- $A_t$ es la **matriz de transición de estado** (evolución natural del sistema).
- $B_t$ es la **matriz de control** (cómo afecta la acción al estado).
- $\varepsilon_t \sim \mathcal{N}(0, R_t)$ es el **ruido de proceso** gaussiano.

![[discrete-kalman-system.png]]
*Sistema discreto del filtro de Kalman.*

### Modelo de observación (sensor)

$$z_t = C_t x_t + \delta_t$$

donde:
- $z_t$ es la **observación** (lectura del sensor).
- $C_t$ es la **matriz de observación** (qué parte del estado es observable).
- $\delta_t \sim \mathcal{N}(0, Q_t)$ es el **ruido de medición** gaussiano.

## 3. Componentes del filtro

![[kalman-components.png]]
*Componentes del filtro de Kalman.*

Las matrices $R_t$ (covarianza del ruido de proceso) y $Q_t$ (covarianza del ruido de sensor)
son parámetros críticos que requieren sintonización. Valores mal calibrados llevan a sobreconfianza o subconfianza.

## 4. Algoritmo — caso 1D intuitivo

### Predicción

![[kalman-1d-prediction-correction.png]]
*Predicción y corrección en el caso 1D.*

La predicción proyecta la creencia hacia adelante usando el modelo de movimiento:

$$\bar{\mu}_t = A_t \mu_{t-1} + B_t u_t$$
$$\bar{\Sigma}_t = A_t \Sigma_{t-1} A_t^\top + R_t$$

La incertidumbre **aumenta** en la predicción (se suma $R_t$), reflejando que el movimiento introduce ruido.

### Corrección

![[kalman-1d-measurement.png]]
*Corrección con medición en el caso 1D.*

La corrección combina la predicción con la nueva observación:

$$K_t = \bar{\Sigma}_t C_t^\top (C_t \bar{\Sigma}_t C_t^\top + Q_t)^{-1}$$
$$\mu_t = \bar{\mu}_t + K_t (z_t - C_t \bar{\mu}_t)$$
$$\Sigma_t = (I - K_t C_t) \bar{\Sigma}_t$$

El término $K_t$ es la **ganancia de Kalman**. Determina cuánto "confiar" en la medición vs la predicción:
- Si $Q_t$ (ruido de sensor) es grande → $K_t$ pequeña → se confía más en la predicción.
- Si $R_t$ (ruido de proceso) es grande → $K_t$ grande → se confía más en la medición.

El término $(z_t - C_t \bar{\mu}_t)$ es la **innovación**: la diferencia entre lo medido y lo esperado.

> [!info] La incertidumbre tras update siempre baja (Thrun et al., §3.2.3)
> Una observación contraintuitiva del libro: el belief después de combinar predicción + medición tiene **menor varianza que cualquiera de los dos contribuyentes individuales** — incluso si la medición sola era ruidosa. Geométricamente: la gaussiana posterior está más concentrada que la gaussiana de la predicción y que la gaussiana de la medición. La razón es que el KF está integrando información de fuentes parcialmente independientes; cada nueva fuente reduce la incertidumbre del estimador combinado. Esta propiedad es la "magia" del Kalman, y desaparece cuando las fuentes están altamente correlacionadas (un caso que el filtro estándar no detecta y que lleva a sobreconfianza).

## 5. Algoritmo completo — pseudocódigo

![[kalman-algorithm-pseudocode.png]]
*Pseudocódigo del filtro de Kalman.*

## 6. Ciclo predict-update visual

![[kalman-predict-cycle.png]]
*Ciclo de predicción del Kalman.*

![[kalman-correct-cycle.png]]
*Ciclo de corrección del Kalman.*

![[kalman-full-cycle.png]]
*Ciclo completo predict-update del Kalman.*

## 7. Propiedades del Kalman

**Optimalidad:** El Kalman minimiza el error cuadrático medio entre todas las estimaciones lineales.
Esto se demuestra por la derivación desde la regla de Bayes con Gaussianas.

**Recursividad:** El filtro solo necesita el estado anterior $(\mu_{t-1}, \Sigma_{t-1})$ y la observación actual.
No almacena el historial completo de mediciones.

**Eficiencia computacional:** $O(d^3)$ por paso (por la inversión de matrices de $d \times d$).
Para $d$ pequeño (típico en robótica: $d = 3$ a $10$), es extremadamente rápido.

> [!info] Costo real con algoritmos modernos (Thrun et al., §3.2.2)
> El libro precisa el costo: la inversión matricial *naive* es $O(d^3)$, pero con los mejores algoritmos baja a $O(d^{2.8})$. Por iteración el KF cuesta $O(k^{2.8})$ donde $k$ es la dimensión del **vector de medición** (no del estado). Cuando $k \ll n$ — caso común en mapeo donde el estado se infla con landmarks pero las mediciones siguen siendo pocas — el costo total queda dominado por las multiplicaciones $O(n^2)$ del paso 6 ($\Sigma_t = (I - K_t C_t) \bar\Sigma_t$). Esto explica por qué el KF escala bien a estados de miles de dimensiones (en SLAM con muchos landmarks) siempre que las matrices $K_t C_t$ y similares se mantengan dispersas.

**Limitaciones:** Solo funciona con sistemas **lineales**. Para sistemas no lineales se necesita [[EKF]] o [[UKF]].

## Conecta con
- ⬅️ [[Gaussiana Multivariada]] — propiedades que hacen posible el Kalman
- ⬅️ [[Filtros Discretos]] — misma estructura predict/correct, diferente representación
- ➡️ [[EKF]] — extensión a sistemas no lineales vía linealización
- ➡️ [[UKF]] — extensión alternativa vía sigma points

## Fuentes
- `Raw/Diapositivas/Teoricas/10-filtro_de_kalman-3.pdf`
  - págs. 1–3 → 1. Introducción
  - págs. 4–5 → 2. Modelo del sistema
  - pág. 10 → 3. Componentes del filtro
  - págs. 11–12 → 4. Algoritmo — caso 1D intuitivo
  - pág. 18 → 5. Algoritmo completo — pseudocódigo
  - págs. 22–24 → 6. Ciclo predict-update visual
- `Raw/Libro/ProbabilisticRobotics.pdf`
  - págs. 34–37 → 3 supuestos lineal-gaussianos (§3.2.1)
  - págs. 37–39 → Integración reduce incertidumbre (§3.2.3)
  - pág. 37 → Costo computacional con algoritmos modernos (§3.2.2)