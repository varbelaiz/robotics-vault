---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Probabilidad Conjunta

> La probabilidad de que dos (o más) eventos ocurran simultáneamente.

## Definición  *(05-intro_robo_proba parte 1, pág. 9)*

$P(X = x \text{ e } Y = y) = P(x, y)$ es la probabilidad conjunta de $x$ e $y$.

### Independencia

Si $X$ e $Y$ son **independientes**:

$$P(x, y) = P(x) \cdot P(y)$$

La probabilidad conjunta es el producto de las marginales.

## Marginalización  *(05-intro_robo_proba parte 1, pág. 12)*

### Caso discreto

$$P(x) = \sum_{y} P(x, y)$$

### Caso continuo

$$p(x) = \int P(x, y) \, dy$$

La marginalización permite obtener la distribución de una variable a partir de la distribución conjunta.

## Ley de la Probabilidad Total  *(05-intro_robo_proba parte 1, pág. 11)*

Combinación de la regla de probabilidad total y marginalización:

- **Discreto**: $P(y) = \sum_{x} P(y \mid x) P(x)$
- **Continuo**: $p(y) = \int p(y \mid x) p(x) \, dx$

## Conexiones
- [[Probabilidad Condicional]] — $P(x \mid y) = P(x, y) / P(y)$
- [[Regla de Bayes]] — reescritura de la condicional usando la conjunta
- [[Filtro de Bayes]] — usa marginalización en el paso de predicción

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` — págs. 9, 11, 12
