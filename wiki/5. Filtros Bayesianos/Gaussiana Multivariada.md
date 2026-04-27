---
modulo: 5. Filtros Bayesianos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/09-filtro-de-particulas-mcl-4.pdf
  - Raw/Diapositivas/Teoricas/10-filtro_de_kalman-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[Filtros Bayesianos|← Filtros Bayesianos]] | [[Robotica|← Inicio]]

# Gaussiana Multivariada

> Distribución de probabilidad continua sobre un espacio vectorial, definida completamente
> por su media y su matriz de covarianza. Base de todos los filtros Kalman.

## Prerequisitos
- [[Probabilidad - Repaso|Distribución Gaussiana]]
- [[Algebra Lineal|Matrices y operaciones]]
- [[Regla de Bayes]]

## 1. Definición y notación

La **distribución Gaussiana multivariada** en un espacio $d$-dimensional se escribe:

$$\mathcal{N}(\mathbf{x}; \boldsymbol{\mu}, \boldsymbol{\Sigma}) = \frac{1}{(2\pi)^{d/2} |\boldsymbol{\Sigma}|^{1/2}} \exp\left( -\frac{1}{2} (\mathbf{x} - \boldsymbol{\mu})^\top \boldsymbol{\Sigma}^{-1} (\mathbf{x} - \boldsymbol{\mu}) \right)$$

donde:
- $\boldsymbol{\mu} \in \mathbb{R}^d$ es el **vector media**.
- $\boldsymbol{\Sigma} \in \mathbb{R}^{d \times d}$ es la **matriz de covarianza** (simétrica, definida positiva).
- $|\boldsymbol{\Sigma}|$ es el determinante.
- El término $(\mathbf{x} - \boldsymbol{\mu})^\top \boldsymbol{\Sigma}^{-1} (\mathbf{x} - \boldsymbol{\mu})$ es el **cuadrado de la distancia de Mahalanobis**.

![[gaussian-multi.png]]
*Gaussiana multivariada y sus parámetros.*

La densidad es máxima en $\mathbf{x} = \boldsymbol{\mu}$ y decae exponencialmente con la distancia de Mahalanobis.

## 2. Propiedades clave

Las Gaussianas tienen propiedades algebraicas cerradas que las hacen ideales para filtrado recursivo:

![[gaussian-properties.png]]
*Propiedades de la distribución Gaussiana.*

### Marginales
Proyectar una Gaussiana multivariada sobre un subconjunto de dimensiones produce otra Gaussiana.
Si $\mathbf{x} = [\mathbf{x}_a^\top, \mathbf{x}_b^\top]^\top \sim \mathcal{N}(\boldsymbol{\mu}, \boldsymbol{\Sigma})$, entonces:

$$\mathbf{x}_a \sim \mathcal{N}(\boldsymbol{\mu}_a, \boldsymbol{\Sigma}_{aa})$$

### Condicionales
Condicional de una Gaussiana es Gaussiana:

$$\mathbf{x}_a | \mathbf{x}_b \sim \mathcal{N}(\boldsymbol{\mu}_a + \boldsymbol{\Sigma}_{ab} \boldsymbol{\Sigma}_{bb}^{-1} (\mathbf{x}_b - \boldsymbol{\mu}_b), \; \boldsymbol{\Sigma}_{aa} - \boldsymbol{\Sigma}_{ab} \boldsymbol{\Sigma}_{bb}^{-1} \boldsymbol{\Sigma}_{ba})$$

### Suma de Gaussianas (mezcla con pesos fijos)
La suma ponderada de Gaussianas con el mismo $\boldsymbol{\Sigma}$ es Gaussiana:

$$\sum_i w_i \mathcal{N}(\mathbf{x}; \boldsymbol{\mu}_i, \boldsymbol{\Sigma}) = \mathcal{N}(\mathbf{x}; \sum_i w_i \boldsymbol{\mu}_i, \boldsymbol{\Sigma})$$

### Producto de Gaussianas (Bayes con prior y likelihood Gaussianos)

$$\mathcal{N}(\mathbf{x}; \boldsymbol{\mu}_1, \boldsymbol{\Sigma}_1) \cdot \mathcal{N}(\mathbf{x}; \boldsymbol{\mu}_2, \boldsymbol{\Sigma}_2) \propto \mathcal{N}(\mathbf{x}; \boldsymbol{\mu}_{new}, \boldsymbol{\Sigma}_{new})$$

donde:

$$\boldsymbol{\Sigma}_{new} = (\boldsymbol{\Sigma}_1^{-1} + \boldsymbol{\Sigma}_2^{-1})^{-1}$$
$$\boldsymbol{\mu}_{new} = \boldsymbol{\Sigma}_{new} (\boldsymbol{\Sigma}_1^{-1} \boldsymbol{\mu}_1 + \boldsymbol{\Sigma}_2^{-1} \boldsymbol{\mu}_2)$$

Esta propiedad es el **corazón del paso de corrección del Kalman**.

### Transformación lineal
Si $\mathbf{x} \sim \mathcal{N}(\boldsymbol{\mu}, \boldsymbol{\Sigma})$ y $\mathbf{y} = \mathbf{A}\mathbf{x} + \mathbf{b}$, entonces:

$$\mathbf{y} \sim \mathcal{N}(\mathbf{A}\boldsymbol{\mu} + \mathbf{b}, \; \mathbf{A}\boldsymbol{\Sigma}\mathbf{A}^\top)$$

Esta propiedad es el **corazón del paso de predicción del Kalman**.

## 3. Interpretación geométrica

Los contornos de densidad constante de una Gaussiana multivariada son **elipsoides** en $\mathbb{R}^d$.
Los ejes del elipsoide están dados por los autovalores y autovectores de $\boldsymbol{\Sigma}$:

- El autovalor $\lambda_i$ más grande indica la dirección de mayor incertidumbre.
- El autovalor $\lambda_i$ más pequeño indica la dirección de menor incertidumbre.
- Si $\boldsymbol{\Sigma}$ es diagonal, no hay correlación entre dimensiones.

El elipsoide de $1\sigma$ contiene ≈68% de la masa (en 1D). El de $2\sigma$ ≈95%.

## 4. Por qué Gaussianas en filtrado

La razón fundamental es la **clausura algebraica**: si el prior es Gaussiano, el modelo de movimiento es lineal
con ruido Gaussiano, y el modelo de sensor es lineal con ruido Gaussiano, entonces la posterior es Gaussiana.
Esto permite representar la creencia completa con solo dos parámetros $(\boldsymbol{\mu}, \boldsymbol{\Sigma})$,
en lugar de un vector de millones de entradas (grilla) o miles de partículas (MCL).

Esta clausura es lo que permite el [[Filtro de Kalman]] ser **óptimo** (no aproximado) bajo estos supuestos.

## Conecta con
- ⬅️ [[Probabilidad - Repaso]] — base estadística
- ⬅️ [[Algebra Lineal|Matrices y operaciones]] — manipulación de $\boldsymbol{\Sigma}$
- ➡️ [[Filtro de Kalman]] — usa estas propiedades como base
- ➡️ [[EKF]] — linealiza para aplicar Gaussianas a sistemas no lineales
- ➡️ [[Puntos Sigma]] — método del UKF para propagar Gaussianas sin linealización

## Fuentes
- `Raw/Diapositivas/Teoricas/10-filtro_de_kalman-3.pdf`
  - págs. 3–5 → 1. Definición y notación
  - págs. 6–7 → 2. Propiedades clave
  - pág. 6 → 3. Interpretación geométrica