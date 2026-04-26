---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Probabilidad Condicional

> La probabilidad de un evento dado que otro evento ha ocurrido.

## Definición  *(05-intro_robo_proba parte 1, pág. 10)*

$P(x \mid y)$ es la probabilidad de $x$ dado $y$:

$$P(x \mid y) = \frac{P(x, y)}{P(y)}$$

### Regla de reescritura

$$P(x, y) = P(x \mid y) \cdot P(y)$$

### Independencia

Si $X$ e $Y$ son independientes, entonces:

$$P(x \mid y) = P(x)$$

El conocimiento de $y$ no modifica la probabilidad de $x$.

## Causal vs. Diagnóstico  *(05-intro_robo_proba parte 1, págs. 18–19)*

En robótica hay dos direcciones de razonamiento:

- **Causal**: $P(z \mid x)$ — dada la posición, ¿qué medición espero? (fácil de obtener)
- **Diagnóstico**: $P(x \mid z)$ — dada la medición, ¿dónde estoy? (difícil de calcular directamente)

La **Regla de Bayes** permite convertir conocimiento causal en razonamiento diagnóstico.

### Ejemplo: puerta

Un robot mide si una puerta está abierta o cerrada con un sensor imperfecto:
- $P(z \mid \text{abierta}) = 0.6$
- $P(z \mid \neg\text{abierta}) = 0.3$
- $P(\text{abierta}) = P(\neg\text{abierta}) = 0.5$

Aplicando Bayes: $P(\text{abierta} \mid z) = \frac{0.6 \cdot 0.5}{0.6 \cdot 0.5 + 0.3 \cdot 0.5} = \frac{0.3}{0.45} = \frac{2}{3}$

La medición $z$ eleva la probabilidad de que la puerta esté abierta.

## Conexiones
- [[Regla de Bayes]] — inversión de la condicional
- [[Probabilidad Conjunta]] — base para definir la condicional
- [[Modelo de Sensor]] — $P(z \mid x)$ es una probabilidad condicional

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` — págs. 10, 18–19
