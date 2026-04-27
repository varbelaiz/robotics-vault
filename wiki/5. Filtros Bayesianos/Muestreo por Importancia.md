---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/09-filtro-de-particulas-mcl-4.pdf
ultima_actualizacion: 2026-04-26
---

> [[Filtros Bayesianos|← Filtros Bayesianos]] | [[Robotica|← Inicio]]

# Muestreo por Importancia

> Técnica para aproximar una distribución objetivo difícil de muestrear,
> muestreando desde una distribución propuesta más simple y reponderando.

## Prerequisitos
- [[Probabilidad - Repaso|Muestreo aleatorio]]
- [[Regla de Bayes]]
- [[Filtros Discretos]]

## 1. Problema y motivación

En el filtro Bayesiano, el paso de corrección requiere evaluar:

$$b(x_t) = \eta \, P(z_t | x_t) \, \sum_{x_{t-1}} P(x_t | x_{t-1}, u_t) \, b(x_{t-1})$$

Para espacios continuos, esta suma es una integral que no tiene forma cerrada en general.
El **muestreo por importancia** ofrece una forma de aproximar $b(x_t)$ sin evaluar la integral directamente.

![[importance-sampling-principle.png]]
*Principio del muestreo por importancia.*

La idea central: en lugar de muestrear directamente de $b(x_t)$ (difícil), muestreamos
de una distribución propuesta $p(x_t)$ (fácil) y asignamos **pesos de importancia**:

$$w_i = \frac{b(x_t^{(i)})}{p(x_t^{(i)})} \propto \frac{P(z_t | x_t^{(i)}) \, b'(x_t^{(i)})}{p(x_t^{(i)})}$$

## 2. Rejection Sampling vs Importance Sampling

### Rejection Sampling

El método más simple: generar muestras de una distribución propuesta uniforme y aceptar/rechazar
según la altura de la densidad objetivo.

![[rejection-sampling.png]]
*Rejection sampling: genera puntos aleatorios y los acepta si quedan bajo la curva.*

**Problema:** es extremadamente ineficiente para distribuciones multimodales o con regiones de alta probabilidad
en un espacio grande —la mayoría de las muestras se rechazan.

### Importance Sampling

En lugar de rechazar muestras, las **repondera** con pesos de importancia:

![[importance-sampling-visual.png]]
*Importance sampling: todas las muestras se mantienen, pero con pesos diferentes.*

Cada partícula $x^{(i)}$ se pondera con $w^{(i)} \propto P(z_t | x^{(i)}) \, b'(x^{(i)}) / p(x_t^{(i)})$.
La aproximación final es:

$$b(x_t) \approx \sum_i w^{(i)} \, \delta(x_t - x^{(i)})$$

donde $\delta$ es la delta de Dirac.

![[weighted-samples-math.png]]
*Matemática de las muestras ponderadas.*

## 3. Propiedades

La calidad de la aproximación depende de la elección de la distribución propuesta:

- Si $p(x_t)$ es "cercana" a $b(x_t)$, los pesos serán uniformes y la aproximación será precisa con pocas partículas.
- Si $p(x_t)$ es muy diferente, algunas partículas tendrán pesos desproporcionadamente altos y la mayoría pesos cercanos a cero.

![[importance-resampling-example.png]]
*Ejemplo de reponderación: partículas cercanas a la verdad reciben mayor peso.*

![[importance-resampling-visual.png]]
*Visualización del proceso de reponderación.*

### Convergencia

Bajo condiciones generales (mismo soporte, etc.), el muestreo por importancia converge en media
a la distribución verdadera a medida que $N \to \infty$. La tasa de convergencia es $O(1/\sqrt{N})$.

## 4. Conexión con MCL

En el filtro de partículas (MCL), la distribución propuesta más común es el **motion model** mismo:

$$p(x_t) = P(x_t | u_t, z_{1:t-1})$$

con lo que los pesos se simplifican a:

$$w^{(i)} \propto P(z_t | x_t^{(i)})$$

Esta elección es natural porque el motion model ya se sabe evaluar y muestrear eficientemente.

## Conecta con
- ⬅️ [[Filtros Discretos]] — el filtro Bayesiano que necesita aproximarse
- ➡️ [[MCL - Filtro de Partículas]] — aplica muestreo por importancia en localización
- ➡️ [[Puntos Sigma]] — el UKF usa un "muestreo determinista" análogo (sigma points)

## Fuentes
- `Raw/Diapositivas/Teoricas/09-filtro-de-particulas-mcl-4.pdf`
  - págs. 7–8 → 1. Problema y motivación
  - págs. 8–9 → 2. Rejection Sampling vs Importance Sampling
  - págs. 10–13 → 3. Propiedades