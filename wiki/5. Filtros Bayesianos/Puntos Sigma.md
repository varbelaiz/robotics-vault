---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/11b-filtro_ukf-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[Filtros Bayesianos|← Filtros Bayesianos]] | [[Robotica|← Inicio]]

# Puntos Sigma

> Conjunto de $2d + 1$ puntos deterministas que capturan exactamente la media y covarianza
> de una Gaussiana multivariada, base del Unscented Transform.

## Prerequisitos
- [[Gaussiana Multivariada]]
- [[Descomposición de Cholesky]]

## 1. Concepto

Los **puntos sigma** son un conjunto pequeño de puntos seleccionados estratégicamente alrededor
de la media de una Gaussiana, tales que:
1. Su media ponderada es exactamente $\mu$.
2. Su covarianza ponderada es exactamente $\Sigma$.

Para una Gaussiana en $\mathbb{R}^d$, se necesitan exactamente $2d + 1$ puntos.

![[ukf-sigma-points-selection.png]]
*Selección de los sigma points alrededor de la media.*

### Selección

Dada una Gaussiana $\mathcal{N}(\mu, \Sigma)$ en $\mathbb{R}^d$:

$$\chi_0 = \mu$$

$$\chi_i = \mu + (\sqrt{(d + \lambda) \Sigma})_i \quad \text{para } i = 1, ..., d$$

$$\chi_i = \mu - (\sqrt{(d + \lambda) \Sigma})_i \quad \text{para } i = d+1, ..., 2d$$

donde $(\sqrt{(d + \lambda) \Sigma})_i$ es la $i$-ésima columna de la matriz raiz.

La matriz raiz se obtiene por [[Descomposición de Cholesky]]: $\Sigma = LL^\top$, entonces $\sqrt{\Sigma} = L$.

![[ukf-sigma-cholesky.png]]
*Cholesky para obtener la matriz raiz en los sigma points.*

![[ukf-sigma-eigenvalues.png]]
*Relación con los autovalores de la covarianza.*

## 2. Propagación a través de funciones no lineales

La clave del Unscented Transform es que los sigma points se propagan **directamente**
a través de la función no lineal, sin linearización:

![[ukf-sigma-transform.png]]
*Transformación directa de los sigma points.*

$$\gamma_i = f(\chi_i)$$

Después de la transformación, se reconstruye la distribución:

![[ukf-sigma-reconstruct.png]]
*Reconstrucción de la distribución desde los puntos transformados.*

$$\mu_{new} = \sum_{i=0}^{2d} W_i^{(m)} \gamma_i$$

$$\Sigma_{new} = \sum_{i=0}^{2d} W_i^{(c)} (\gamma_i - \mu_{new})(\gamma_i - \mu_{new})^\top$$

## 3. Pesos

![[ukf-sigma-weights.png]]
*Pesos para la reconstrucción.*

Los pesos $W_i^{(m)}$ (para la media) y $W_i^{(c)}$ (para la covarianza) se calculan a partir
de los parámetros $\alpha$, $\beta$, $\kappa$ del [[UKF]].

## 4. Por qué funciona

Los sigma points capturan los **dos primeros momentos** (media y covarianza) de la Gaussiana
de forma exacta. Al propagarlos a través de una función no lineal, la media y covarianza
reconstruidas son aproximaciones de orden $O(h^3)$ —mejor que la aproximación $O(h)$ del EKF.

![[ukf-summary-transform.png]]
*Resumen del proceso completo de sigma points.*

En esencia, los puntos sigma son un **muestreo determinista** análogo al muestreo por importancia
([[Muestreo por Importancia]]), pero con el número mínimo de puntos necesarios para capturar
los dos primeros momentos.

## Conecta con
- ⬅️ [[Descomposición de Cholesky]] — para calcular la matriz raiz
- ⬅️ [[Gaussiana Multivariada]] — distribución que los sigma points representan
- ➡️ [[UKF]] — aplica los sigma points en el filtro completo

## Fuentes
- `Raw/Diapositivas/Teoricas/11b-filtro_ukf-3.pdf`
  - págs. 3–7 → 1. Concepto
  - págs. 12–13 → 3. Pesos