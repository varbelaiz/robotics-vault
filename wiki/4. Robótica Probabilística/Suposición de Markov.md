---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Suposición de Markov

> El estado actual resume toda la historia pasada. Condición clave para la recursividad en filtros bayesianos.

## Definición  *(05-intro_robo_proba parte 1, pág. 21; parte 2, pág. 32)*

![[proba - suposicion markov.png]]
*La suposición de Markov: la observación actual depende solo del estado actual.*

La **suposición de Markov** establece que la observación $z_t$ es independiente de todas las observaciones pasadas $z_1, \ldots, z_{t-1}$ y acciones pasadas $u_1, \ldots, u_{t-1}$, dado el estado actual $x_t$:

$$P(z_t \mid x_t, z_{1:t-1}, u_{1:t}) = P(z_t \mid x_t)$$

De forma similar, el estado futuro solo depende del estado actual y la acción actual:

$$P(x_t \mid x_{t-1}, x_{t-2}, \ldots, u_t, u_{t-1}, \ldots) = P(x_t \mid x_{t-1}, u_t)$$

## Suposiciones subyacentes  *(05-intro_robo_proba parte 2, pág. 32)*

Para que la suposición de Markov sea válida se requiere:

1. **Mundo estático**: el entorno no cambia por sí solo entre pasos
2. **Ruido independiente**: el ruido en mediciones y acciones es independiente entre pasos de tiempo
3. **Modelo perfecto**: no hay errores de aproximación en los modelos de sensor y acción

## ¿Por qué importa?

Sin la suposición de Markov, el robot necesitaría recordar toda la historia de observaciones y acciones para estimar su estado actual. Con Markov, bastan:
- El **belief** actual $Bel(x_{t-1})$ (que resume toda la historia pasada)
- La **acción** $u_t$
- La **observación** $z_t$

Esto hace posible el **filtro recursivo**.

## Conexiones
- [[Filtro de Bayes]] — usa Markov para derivar el algoritmo recursivo
- [[Filtros Discretos]] — implementación en grilla con suposición Markoviana
- [[MCL - Filtro de Partículas]] — también asume Markov

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` — pág. 21
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf` — pág. 32
