---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Filtro de Bayes

> Algoritmo recursivo para estimar el estado (belief) de un sistema dinámico dado observaciones y acciones.

## Problema  *(05-intro_robo_proba parte 2, pág. 31)*

![[proba - filtro bayes estructura.png]]
*Estructura del filtro de Bayes: entradas y salida.*

**Dado**:
- Una serie de observaciones $z_1, \ldots, z_t$ y acciones $u_1, \ldots, u_t$
- Modelo de sensor: $P(z_t \mid x_t)$
- Modelo de acción (transición): $P(x_t \mid u_t, x_{t-1})$
- Probabilidad a priori: $P(x_0)$

**Se desea**:
- Estimar el estado $X$ del sistema en cada paso
- La probabilidad a posteriori (**belief**): $Bel(x_t) = P(x_t \mid z_{1:t}, u_{1:t})$

## Derivación del algoritmo recursivo  *(05-intro_robo_proba parte 2, págs. 33–40)*

![[proba - filtro bayes derivacion 1.png]]
*Primeros pasos de la derivación del filtro de Bayes.*

![[proba - filtro bayes derivacion 2.png]]
*Desarrollo intermedio usando Bayes y Markov.*

![[proba - filtro bayes algoritmo.png]]
*Algoritmo completo del filtro de Bayes.*

El filtro tiene dos pasos:

### 1. Predicción (acción)

Cuando el robot ejecuta una acción $u_t$:

$$\bar{Bel}(x_t) = \int P(x_t \mid u_t, x_{t-1}) Bel(x_{t-1}) \, dx_{t-1}$$

La acción **aumenta** la incertidumbre (el robot no sabe exactamente dónde quedó).

### 2. Actualización (percepción)

Cuando el robot toma una medición $z_t$:

$$Bel(x_t) = \eta P(z_t \mid x_t) \bar{Bel}(x_t)$$

La percepción **reduce** la incertidumbre.

## Algoritmo

```
Filtro_de_Bayes(Bel(x), d):
    η = 0
    Si d es una medición z:
        Para cada x:
            Bel'(x) = P(z | x) * Bel(x)
            η = η + Bel'(x)
        Para cada x:
            Bel'(x) = η * Bel'(x)
    Si d es una acción u:
        Para cada x:
            Bel'(x) = Σ_x' P(x | u, x') * Bel(x')
    Retornar Bel'(x)
```

## Usos del filtro de Bayes  *(05-intro_robo_proba parte 2, pág. 42)*

El filtro de Bayes es la base de muchas herramientas:

- **Filtros de Kalman** — sistemas lineales gaussianos
- **Filtros de Partículas** (MCL) — representación con muestras
- **Modelos Ocultos de Markov** — estados no observables directamente
- **Redes Bayesianas Dinámicas** — modelos gráficos temporales
- **POMDPs** — procesos de decisión bajo incertidumbre

## Resumen  *(05-intro_robo_proba parte 2, pág. 45)*

- La regla de Bayes permite calcular probabilidades que de otra forma serían complejas
- Con la suposición de Markov, la actualización recursiva es eficiente
- Los filtros de Bayes estiman el estado de un sistema dinámico de forma recursiva

## Conexiones
- [[Regla de Bayes]] — base matemática
- [[Suposición de Markov]] — condición de independencia temporal
- [[Modelo de Movimiento]] — $P(x_t \mid u_t, x_{t-1})$
- [[Modelo de Sensor]] — $P(z_t \mid x_t)$
- [[Filtros Discretos]] — primera implementación práctica
- [[Filtro de Kalman]] — versión para sistemas lineales gaussianos
- [[MCL - Filtro de Partículas]] — versión con representación por muestras

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf` — págs. 31–45
