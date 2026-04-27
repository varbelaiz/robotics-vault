---
modulo: 1. Fundamentos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Fundamentos|← Fundamentos]] | [[Robotica|← Inicio]]

# Matriz Definida Positiva

> Análoga matricial al "número positivo". Aparece en robótica como **matriz de covarianza** de distribuciones gaussianas — todo el ruido modelado en filtros bayesianos vive como matrices definidas positivas.

## Prerequisitos
- [[Matrices]] (en particular determinante, autovalores, traza)

## 1. Definición  *(Teóricas 01-algebra_lineal, slide 21)*

Una matriz $M$ es **definida positiva**, notado $M > 0$, sii:

$$
z^T M z > 0 \quad \forall z \neq 0
$$

Es decir, la forma cuadrática asociada es estrictamente positiva para cualquier vector no nulo.

**Ejemplo** — la identidad es definida positiva:

$$
M_1 = \begin{pmatrix} 1 & 0 \\ 0 & 1 \end{pmatrix}, \quad
\begin{pmatrix} z_1 & z_2 \end{pmatrix} \begin{pmatrix} 1 & 0 \\ 0 & 1 \end{pmatrix} \begin{pmatrix} z_1 \\ z_2 \end{pmatrix} = z_1^2 + z_2^2 > 0
$$

![[Matriz definida positiva - definicion.png]]
*Definición formal y ejemplo con la matriz identidad. Slide 21.*

## 2. Propiedades  *(Teóricas 01-algebra_lineal, slide 22)*

Toda matriz definida positiva cumple:

- Es **invertible**, y su inversa también es definida positiva.
- Todos sus **autovalores reales son $> 0$**.
- La **traza es $> 0$** (porque la traza es la suma de los autovalores).
- Admite **descomposición de Cholesky**: $A = L L^T$, con $L$ triangular inferior.

![[Matriz definida positiva - propiedades.png]]
*Propiedades clave: invertibilidad, autovalores positivos, traza positiva, Cholesky. Slide 22.*

## 3. Variantes y conexiones
- Las **matrices de covarianza** de distribuciones gaussianas son siempre definidas positivas → [[Filtro de Kalman]] (matrices $R_t$, $Q_t$, $\Sigma_t$).
- La descomposición de Cholesky habilita el **muestreo eficiente** de gaussianas multivariadas (útil en [[MCL - Filtro de Partículas|Monte Carlo Localization]]).

## Fuentes
- `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf` — slides 21–22.
