---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Variables Aleatorias Discretas

> Una variable aleatoria que toma un número contable de valores, con una función de masa de probabilidad.

## Definición  *(05-intro_robo_proba parte 1, págs. 6, 8)*

![[proba - variables aleatorias discretas.png]]
*Variable aleatoria discreta con función de masa de probabilidad.*

- $X$ denota una variable aleatoria
- $X$ toma un número contable de valores $\{x_1, x_2, \ldots, x_n\}$
- $P(X = x_i)$ o $P(x_i)$ es la probabilidad de que $X$ tome el valor $x_i$
- $P(\cdot)$ es la **función de masa de probabilidad** (PMF)

### Normalización  *(05-intro_robo_proba parte 1, pág. 8)*

$$\sum_{i=1}^{n} P(x_i) = 1$$

La suma de todas las probabilidades debe ser 1.

## Ejemplo

En robótica, el estado discreto puede ser:
- Puerta: $\{$abierta, cerrada$\}$
- Celda de un mapa de grilla: $\{$ocupada, libre$\}$
- Posición en una grilla finita: $\{c_1, c_2, \ldots, c_N\}$

## Conexiones
- [[Variables Aleatorias Continuas]] — versión continua con densidad
- [[Filtros Discretos]] — filtro de Bayes sobre variables discretas
- [[Mapas de Grilla]] — representación discreta del espacio

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` — págs. 6, 8
