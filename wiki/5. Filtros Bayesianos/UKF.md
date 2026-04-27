---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/11b-filtro_ukf-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[Filtros Bayesianos|← Filtros Bayesianos]] | [[Robotica|← Inicio]]

# UKF

> Kalman Filter no lineal sin linealización: propaga una Gaussiana mediante un conjunto
> pequeño de puntos deterministas (sigma points) a través de las funciones no lineales.

## Prerequisitos
- [[Filtro de Kalman]]
- [[EKF]]
- [[Puntos Sigma]]
- [[Descomposición de Cholesky]]
- [[Gaussiana Multivariada]]

## 1. Motivación

El [[EKF]] lineariza las funciones no lineales con series de Taylor de primer orden. Esto introduce
errores de truncación que pueden hacer la covarianza inconsistente.

El **Unscented Kalman Filter** (UKF) toma un enfoque diferente: en lugar de linearizar,
seleciona un conjunto pequeño de puntos de muestreo (**sigma points**) que capturan exactamente
la media y covarianza de la distribución Gaussiana, los propaga a través de la función no lineal
directamente, y luego reconstruye la media y covarianza a partir de los puntos transformados.

![[ukf-kf-ekf-comparison.png]]
*Comparación: KF (lineal), EKF (linearización), UKF (sigma points).*

## 2. Idea central

El UKF usa la **Unscented Transform** (UT), que se basa en esta observación:

> Una Gaussiana en $\mathbb{R}^d$ queda completamente determinada por su media y covarianza.
> Podemos capturar estos dos momentos con exactamente $2d + 1$ puntos seleccionados estratégicamente.

### Paso 1: Selección de sigma points

![[ukf-sigma-points-selection.png]]
*Selección de los sigma points.*

Dada la media $\mu$ y covarianza $\Sigma$, se generan $2d + 1$ sigma points:

$$\chi_0 = \mu$$
$$\chi_i = \mu + (\sqrt{(d + \lambda) \Sigma})_i \quad \text{para } i = 1, ..., d$$
$$\chi_i = \mu - (\sqrt{(d + \lambda) \Sigma})_i \quad \text{para } i = d+1, ..., 2d$$

donde $(\sqrt{(d + \lambda) \Sigma})_i$ denota la $i$-ésima columna (o fila) de la matriz raiz.

### Paso 2: Transformación no lineal

![[ukf-sigma-transform.png]]
*Transformación de los sigma points a través de la función no lineal.*

Cada sigma point se propaga a través de la función no lineal $f$:

$$\gamma_i = f(\chi_i)$$

**No hay linearización.** La función se evalúa directamente en cada punto.

### Paso 3: Reconstrucción

![[ukf-sigma-reconstruct.png]]
*Reconstrucción de media y covarianza desde los puntos transformados.*

$$\mu_{new} = \sum_{i=0}^{2d} W_i^{(m)} \gamma_i$$
$$\Sigma_{new} = \sum_{i=0}^{2d} W_i^{(c)} (\gamma_i - \mu_{new})(\gamma_i - \mu_{new})^\top$$

Los pesos $W_i^{(m)}$ y $W_i^{(c)}$ se eligen para que la media y covarianza reconstruidas
coincidan exactamente con las originales (antes de la transformación).

## 3. Cálculo de la matriz raiz

La matriz raiz $\sqrt{\Sigma}$ se calcula mediante [[Descomposición de Cholesky]]:

![[ukf-sigma-cholesky.png]]
*Descomposición de Cholesky para el cálculo de sigma points.*

$\Sigma = L L^\top$, donde $L$ es triangular inferior. Entonces $\sqrt{\Sigma} = L$.

![[ukf-sigma-eigenvalues.png]]
*Autovalores y la matriz de covarianza.*

Alternativamente, se puede usar la descomposición en autovalores: $\Sigma = V \Lambda V^\top$,
entonces $\sqrt{\Sigma} = V \sqrt{\Lambda}$.

## 4. Pesos

![[ukf-sigma-weights.png]]
*Pesos para la reconstrucción de media y covarianza.*

Los pesos dependen del parámetro $\lambda$:

$$W_0^{(m)} = \frac{\lambda}{d + \lambda}$$
$$W_i^{(m)} = \frac{1}{2(d + \lambda)} \quad \text{para } i > 0$$
$$W_0^{(c)} = \frac{\lambda}{d + \lambda} + (1 - \alpha^2 + \beta)$$
$$W_i^{(c)} = \frac{1}{2(d + \lambda)} \quad \text{para } i > 0$$

## 5. Resumen de la Unscented Transform

![[ukf-summary-transform.png]]
*Resumen del proceso de la Unscented Transform.*

![[ukf-parameters.png]]
*Parámetros del UKF: alpha, beta, kappa.*

### Parámetros del UKF

- **$\alpha$** (típicamente $10^{-4}$): controla la dispersión de los sigma points alrededor de la media.
  Valores pequeños concentran los puntos cerca de $\mu$; valores grandes los dispersan más.
- **$\beta$** (típicamente $2$): incorpora conocimiento previo sobre la distribución.
  $\beta = 2$ es óptimo para distribuciones Gaussianas.
- **$\kappa$** (típicamente $0$ a $3$): parámetro secundario de escalado.
  $\lambda = \alpha^2(d + \kappa) - d$.

## 6. Algoritmo UKF completo

### Predicción
1. Calcular sigma points desde $(\mu_{t-1}, \Sigma_{t-1})$.
2. Propagar cada sigma point a través de $f(\cdot, u_t)$.
3. Reconstruir $(\bar{\mu}_t, \bar{\Sigma}_t)$ de los puntos transformados, sumando $R_t$.

### Corrección
1. Calcular sigma points desde $(\bar{\mu}_t, \bar{\Sigma}_t)$.
2. Propagar cada sigma point a través de $h(\cdot)$.
3. Reconstruir la media de predicción de observación y su covarianza.
4. Calcular la covarianza cruzada entre estado y observación.
5. Calcular la ganancia de Kalman y actualizar $(\mu_t, \Sigma_t)$.

## 7. Comparación EKF vs UKF

![[ukf-vs-ekf-comparison.png]]
*Comparación directa: EKF vs UKF.*

| Aspecto | EKF | UKF |
|---|---|---|
| Linearización | Sí, Taylor 1er orden | No |
| Precisión | Orden $O(h)$ | Orden $O(h^3)$ |
| Coste computacional | Jacobiano + matriz | $2d+1$ evaluaciones de $f$ |
| Jacobiano analítico | Necesario | No necesario |
| Covarianza | Puede ser inconsistente | Generalmente más consistente |
| Convergencia | Puede divergir si $f$ es muy no lineal | Más robusto |

![[ukf-vs-ekf-movement.png]]
*Comparación del modelo de movimiento: EKF vs UKF.*

En la práctica, el UKF es preferible cuando:
- La no linealidad es **fuerte** (la linealización de Taylor es mala).
- Calcular Jacobianos analíticos es **laborioso** o propenso a errores.
- Se necesita **consistencia** en la covarianza estimada.

El EKF sigue siendo útil cuando:
- La no linealidad es **débil** (la aproximación lineal es buena).
- El estado es de **alta dimensión** ($2d+1$ evaluaciones del UKF es caro).
- Se dispone de Jacobianos ya calculados.

## Conecta con
- ⬅️ [[EKF]] — alternativa que usa linearización en lugar de sigma points
- ⬅️ [[Puntos Sigma]] — concepto base del UKF
- ⬅️ [[Descomposición de Cholesky]] — para calcular la matriz raiz
- ⬅️ [[Gaussiana Multivariada]] — distribución que se propaga

## Fuentes
- `Raw/Diapositivas/Teoricas/11b-filtro_ukf-3.pdf`
  - págs. 1–2 → 1. Motivación
  - págs. 3–7 → 2. Idea central
  - págs. 8–11 → 3. Cálculo de la matriz raiz
  - págs. 12–13 → 4. Pesos
  - págs. 14–16 → 5. Resumen de la Unscented Transform
  - págs. 27–33 → 7. Comparación EKF vs UKF