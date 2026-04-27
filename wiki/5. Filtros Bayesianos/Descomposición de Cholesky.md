---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/11b-filtro_ukf-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[Filtros Bayesianos|← Filtros Bayesianos]] | [[Robotica|← Inicio]]

# Descomposición de Cholesky

> Factorización de una matriz simétrica definida positiva como $LL^\top$,
> usada para calcular la matriz raiz en los sigma points del UKF.

## Prerequisitos
- [[Algebra Lineal|Matrices y operaciones]]
- [[Gaussiana Multivariada]]

## 1. Definición

La **descomposición de Cholesky** de una matriz simétrica definida positiva $\Sigma \in \mathbb{R}^{d \times d}$
es una factorización de la forma:

$$\Sigma = L L^\top$$

donde $L$ es una **matriz triangular inferior**.

![[ukf-sigma-cholesky.png]]
*Descomposición de Cholesky y su uso en sigma points.*

La matriz $L$ es única si se requiere que las entradas diagonales sean estrictamente positivas.

## 2. Propiedades

- **Existencia:** Toda matriz simétrica definida positiva tiene una descomposición de Cholesky única.
- **Eficiencia:** El algoritmo de Cholesky cuesta $O(d^3/3)$ operaciones — la mitad que la descomposición LU.
- **Estabilidad:** Numéricamente estable para matrices bien condicionadas.

## 3. Uso en el UKF

En el contexto del [[UKF]] y los [[Puntos Sigma]], la descomposición de Cholesky se usa
para calcular la **matriz raiz** de la covarianza:

$$\sqrt{\Sigma} = L$$

Los sigma points se generan como:

$$\chi_i = \mu + (\sqrt{(d + \lambda)} L)_i$$
$$\chi_{d+i} = \mu - (\sqrt{(d + \lambda)} L)_i$$

donde $(\sqrt{(d + \lambda)} L)_i$ es la $i$-ésima columna de $\sqrt{(d + \lambda)} L$.

![[ukf-sigma-eigenvalues.png]]
*Relación con los autovalores.*

## 4. Alternativa: descomposición espectral

En lugar de Cholesky, se puede usar la descomposición en autovalores:

$$\Sigma = V \Lambda V^\top \implies \sqrt{\Sigma} = V \sqrt{\Lambda}$$

donde $V$ contiene los autovectores y $\Lambda$ es diagonal con los autovalores.

Ambas producen una matriz raiz válida, pero Cholesky es más eficiente y estable numéricamente
para matrices bien condicionadas. La descomposición espectral es útil cuando se necesita
interpretar los ejes principales de la distribución (los autovectores).

## 5. Relación con el filtro de Kalman

La descomposición de Cholesky aparece también en el [[Filtro de Kalman]] estándar,
especialmente en variantes numéricamente estables que mantienen la covarianza factorizada
(*square-root Kalman filter*) para evitar la pérdida de simetría por errores de redondeo.

## Conecta con
- ⬅️ [[Algebra Lineal|Matrices y operaciones]] — base matemática
- ➡️ [[Puntos Sigma]] — uso principal en el UKF
- ➡️ [[UKF]] — filtro que depende de esta descomposición

## Fuentes
- `Raw/Diapositivas/Teoricas/11b-filtro_ukf-3.pdf`
  - págs. 8–9 → 1. Definición