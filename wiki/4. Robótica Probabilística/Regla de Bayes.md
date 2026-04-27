---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Regla de Bayes

> Actualización de creencias al recibir nueva evidencia. El corazón de la robótica probabilística.

## Fórmula  *(05-intro_robo_proba parte 1, pág. 13)*

![[proba - formula de bayes.png]]
*La fórmula de Bayes: convierte conocimiento causal en diagnóstico.*

$$P(x \mid z) = \frac{P(z \mid x) P(x)}{P(z)}$$

Donde:
- $P(x)$: **prior** — creencia previa sobre el estado
- $P(z \mid x)$: **verosimilitud** — modelo de sensor, probabilidad de la medición dado el estado
- $P(x \mid z)$: **posterior** — creencia actualizada
- $P(z)$: **evidencia** — factor de normalización

## Normalización  *(05-intro_robo_proba parte 1, pág. 14)*

![[proba - normalizacion.png]]
*El factor de normalización $\eta = 1/P(z)$ asegura que la posterior sume 1.*

La evidencia se calcula como:

- **Discreto**: $P(z) = \sum_{x} P(z \mid x) P(x)$
- **Continuo**: $p(z) = \int p(z \mid x) p(x) \, dx$

En la práctica, se calcula $\eta$ para que la distribución resultante esté normalizada.

## Bayes con conocimiento de fondo  *(05-intro_robo_proba parte 1, pág. 15)*

Cuando hay variables adicionales (conocimiento de fondo $b$):

$$P(x \mid z, b) = \frac{P(z \mid x, b) P(x \mid b)}{P(z \mid b)}$$

## Independencia condicional  *(05-intro_robo_proba parte 1, pág. 16)*

Si $X$ e $Y$ son incondicionalmente dependientes, pueden ser **condicionalmente independientes** dado $Z$:

$$P(x \mid y, z) = P(x \mid z)$$

Esto no implica que $P(y \mid x, z) = P(y \mid z)$ ni que $P(x, y \mid z) = P(x \mid z) P(y \mid z)$ automáticamente.

## Combinando evidencia  *(05-intro_robo_proba parte 1, págs. 20–22)*

¿Cómo incorporar múltiples mediciones $z_1, z_2, \ldots, z_n$?

Con la **suposición de Markov** ($z_n$ es independiente de $z_1, \ldots, z_{n-1}$ dado $x$):

$$P(x \mid z_1, z_2) = \eta P(z_2 \mid x) P(x \mid z_1)$$

### Ejemplo: segunda medición

- $P(z_2 \mid \text{abierta}) = 0.25$
- $P(z_2 \mid \neg\text{abierta}) = 0.3$
- $P(\text{abierta} \mid z_1) = 2/3$

$$P(\text{abierta} \mid z_1, z_2) = \eta \cdot 0.25 \cdot \frac{2}{3} = \eta \cdot \frac{0.5}{3}$$
$$P(\neg\text{abierta} \mid z_1, z_2) = \eta \cdot 0.3 \cdot \frac{1}{3} = \eta \cdot \frac{0.3}{3}$$

La segunda medición **disminuye** la probabilidad de que la puerta esté abierta.

## Conexiones
- [[Filtro de Bayes]] — aplica Bayes recursivamente con acciones entre mediciones
- [[Suposición de Markov]] — permite la actualización recursiva
- [[Tutorial 5 - Bayes]] — ejercicios prácticos
- [[Filtros Discretos]] — implementación en espacio discreto

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` — págs. 13–22
