---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/10-filtro_de_kalman-3.pdf
ultima_actualizacion: 2026-04-26
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

## 1. Introducción  *(10-filtro_de_kalman, págs. 1–3)*

El filtro de Kalman es el **estimador óptimo** (mínimo error cuadrático medio) para sistemas
que cumplen dos condiciones:
1. El modelo de dinámica y el modelo de observación son **lineales**.
2. Los ruidos son **Gaussianos**.

![[kalman-intro.png]]
*Introducción al filtro de Kalman.*

Bajo estos supuestos, el filtro mantiene la creencia como una única Gaussiana multivariada,
representada compactamente por solo dos parámetros: la media y la covarianza.

## 2. Modelo del sistema  *(10-filtro_de_kalman, págs. 4–5)*

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

## 3. Componentes del filtro  *(10-filtro_de_kalman, pág. 10)*

![[kalman-components.png]]
*Componentes del filtro de Kalman.*

Las matrices $R_t$ (covarianza del ruido de proceso) y $Q_t$ (covarianza del ruido de sensor)
son parámetros críticos que requieren sintonización. Valores mal calibrados llevan a sobreconfianza o subconfianza.

## 4. Algoritmo — caso 1D intuitivo  *(10-filtro_de_kalman, págs. 11–12)*

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

## 5. Algoritmo completo — pseudocódigo  *(10-filtro_de_kalman, pág. 18)*

![[kalman-algorithm-pseudocode.png]]
*Pseudocódigo del filtro de Kalman.*

## 6. Ciclo predict-update visual  *(10-filtro_de_kalman, págs. 22–24)*

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

**Limitaciones:** Solo funciona con sistemas **lineales**. Para sistemas no lineales se necesita [[EKF]] o [[UKF]].

## Conecta con
- ⬅️ [[Gaussiana Multivariada]] — propiedades que hacen posible el Kalman
- ⬅️ [[Filtros Discretos]] — misma estructura predict/correct, diferente representación
- ➡️ [[EKF]] — extensión a sistemas no lineales vía linealización
- ➡️ [[UKF]] — extensión alternativa vía sigma points

## Fuentes
- `Raw/Diapositivas/Teoricas/10-filtro_de_kalman-3.pdf` — págs. 1–24
