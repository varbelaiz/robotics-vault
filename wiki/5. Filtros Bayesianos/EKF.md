---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/11-filtro_de_kalman_extendido-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[Filtros Bayesianos|← Filtros Bayesianos]] | [[Robotica|← Inicio]]

# EKF

> Kalman Filter Extendido: aproxima sistemas no lineales linealizando las funciones
> con series de Taylor de primer orden, usando Jacobianos.

## Prerequisitos
- [[Filtro de Kalman]]
- [[Gaussiana Multivariada]]

## 1. Problema con la linealidad

El [[Filtro de Kalman]] estándar solo funciona cuando los modelos de transición y observación son lineales.
Pero en robótica, casi todos los modelos son **no lineales**:

- La cinemática de un robot diferencial es no lineal (depende de funciones seno/coseno de theta).
- Las mediciones de rangos a landmarks son no lineales (distancia euclídea = raiz cuadrada).

![[ekf-nonlinear-system.png]]
*Sistema no lineal: el modelo real del robot.*

![[ekf-linearity-assumption.png]]
*El suposito de linealidad del Kalman estándar.*

Si el modelo es solo "ligeramente no lineal", la aproximación lineal del Kalman puede funcionar razonablemente.
Pero si la no linealidad es significativa, se necesita una extensión.

## 2. Distribuciones no Gaussianas

![[ekf-non-gaussian.png]]
*Cuando la no linealidad distorsiona la Gaussiana.*

Transformaciones no lineales de una Gaussiana producen distribuciones **no Gaussianas**.
El EKF **asume** que la Gaussiana linealizada es una aproximación aceptable.

## 3. Linearización por Taylor

La idea del EKF es **linealizar** las funciones no lineales alrededor de la media estimada.

![[ekf-taylor-linearization.png]]
*Linearización de Taylor de primer orden.*

Para una función $f(x)$, la aproximación de Taylor de primer orden en el punto $x^*$ es:

$$f(x) \approx f(x^*) + \left.\frac{\partial f}{\partial x}\right|_{x^*} (x - x^*)$$

![[ekf-jacobian-review.png]]
*Revisión del Jacobiano.*

En múltiples dimensiones, el gradiente se generaliza al **Jacobiano** $F$, una matriz cuyas entradas son:

$$F_{ij} = \frac{\partial f_i}{\partial x_j}$$

![[ekf-taylor-example.png]]
*Ejemplo de linearización con Taylor.*

El EKF evalúa el Jacobiano en el punto de la **media actual** ($\mu$), no en un punto fijo.
Esto permite que la linealización se adapte a medida que el robot se mueve.

## 4. EKF — modelo linearizado

![[ekf-linearized-functions.png]]
*Funciones linearizadas para el EKF.*

Los modelos no lineales originales:
- Transición: $x_t = f(x_{t-1}, u_t) + \varepsilon_t$
- Observación: $z_t = h(x_t) + \delta_t$

Se linearizan como:
- $f(x, u) \approx f(\mu, u) + F (x - \mu)$ donde $F = \partial f / \partial x |_{\mu}$
- $h(x) \approx h(\mu) + H (x - \mu)$ donde $H = \partial h / \partial x |_{\mu}$

## 5. Algoritmo EKF — Predicción

![[ekf-algorithm-prediction.png]]
*Paso de predicción del EKF.*

1. **Predicción del estado:** $\bar{\mu}_t = f(\mu_{t-1}, u_t)$
2. **Jacobiano de transición:** $F_t = \partial f / \partial x$ evaluado en $(\mu_{t-1}, u_t)$
3. **Predicción de covarianza:** $\bar{\Sigma}_t = F_t \Sigma_{t-1} F_t^\top + R_t$

La diferencia con el KF está en el paso 1 (se usa $f$ no lineal directamente)
y el paso 2 (se calcula el Jacobiano $F_t$ en lugar de usar la matriz $A_t$ fija).

## 6. Algoritmo EKF — Corrección

![[ekf-algorithm-correction.png]]
*Paso de corrección del EKF.*

1. **Jacobiano de observación:** $H_t = \partial h / \partial x$ evaluado en $\bar{\mu}_t$
2. **Innovación:** $y = z_t - h(\bar{\mu}_t)$
3. **Covarianza de innovación:** $S_t = H_t \bar{\Sigma}_t H_t^\top + Q_t$
4. **Ganancia de Kalman:** $K_t = \bar{\Sigma}_t H_t^\top S_t^{-1}$
5. **Actualización del estado:** $\mu_t = \bar{\mu}_t + K_t y$
6. **Actualización de covarianza:** $\Sigma_t = (I - K_t H_t) \bar{\Sigma}_t$

## 7. Ejemplo: modelo de odometría

El modelo de odometría para un robot diferencial es no lineal:

![[ekf-odometry-model.png]]
*Modelo de odometría para robot diferencial.*

Los Jacobianos de este modelo:

![[ekf-odometry-jacobian-state.png]]
*Jacobiano del modelo de odometría respecto al estado.*

![[ekf-odometry-jacobian-control.png]]
*Jacobiano del modelo de odometría respecto al control.*

## 8. Ejemplo: modelo de sensor con landmarks

Si el robot mide la distancia euclídea a un landmark conocido:

![[ekf-landmark-sensor-model.png]]
*Modelo de sensor: medición de distancia a landmark.*

$$h(x) = \sqrt{(x_x - l_x)^2 + (x_y - l_y)^2}$$

El Jacobiano $H$ es el gradiente de esta función:

$$H = \begin{bmatrix} \frac{x_x - l_x}{h(x)} & \frac{x_y - l_y}{h(x)} & 0 \end{bmatrix}$$

![[ekf-correction-innovation.png]]
*Corrección del EKF: innovación y actualización.*

## 9. Resultados y precisión

![[ekf-result-ground-truth.png]]
*Resultado del EKF comparado con la trayectoria real.*

**Limitaciones del EKF:**
- La linealización de primer orden es una **aproximación**. Si la no linealidad es fuerte, el error puede ser significativo.
- Evaluar Jacobianos analíticamente es laborioso y propenso a errores.
- La covarianza estimada puede ser **inconsistente** (subestimar la incertidumbre real).

Estas limitaciones motivan el [[UKF]], que evita la linearización usando sigma points.

## Conecta con
- ⬅️ [[Filtro de Kalman]] — base lineal que el EKF extiende
- ⬅️ [[Gaussiana Multivariada]] — propiedades de propagación Gaussianas
- ➡️ [[UKF]] — alternativa que evita la linearización
- ➡️ [[Puntos Sigma]] — base del UKF

## Fuentes
- `Raw/Diapositivas/Teoricas/11-filtro_de_kalman_extendido-3.pdf`
  - págs. 1–4 → 1. Problema con la linealidad
  - págs. 8–9 → 2. Distribuciones no Gaussianas
  - págs. 12–14 → 3. Linearización por Taylor
  - págs. 15–16 → 4. EKF — modelo linearizado
  - pág. 27 → 5. Algoritmo EKF — Predicción
  - pág. 30 → 6. Algoritmo EKF — Corrección
  - págs. 17–19 → 7. Ejemplo: modelo de odometría
  - págs. 32–34 → 8. Ejemplo: modelo de sensor con landmarks
  - págs. 35+ → 9. Resultados y precisión