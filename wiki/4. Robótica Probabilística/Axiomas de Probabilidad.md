---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Axiomas de Probabilidad

> Los tres axiomas fundamentales de Kolmogorov que sustentan todo el cálculo probabilístico.

## Los tres axiomas

$P(A)$ indica la probabilidad de que la proposición $A$ es verdadera.

1. **No negatividad**: $P(A) \geq 0$ para toda proposición $A$
2. **Normalización**: $P(\text{True}) = 1$ (la certeza tiene probabilidad 1)
3. **Aditividad**: $P(A \lor B) = P(A) + P(B) - P(A \land B)$

## Detalle del Axioma 3

![[proba - axioma 3 aditividad.png]]
*El axioma de aditividad: la probabilidad de la unión de dos eventos.*

Si $A$ y $B$ son **mutuamente excluyentes** ($A \land B = \text{False}$), entonces $P(A \lor B) = P(A) + P(B)$.

## Consecuencias

- $P(\text{False}) = 0$
- $P(\neg A) = 1 - P(A)$
- Si $A \implies B$, entonces $P(A) \leq P(B)$

## Conexiones
- Base para todas las reglas que siguen: [[Probabilidad Condicional]], [[Regla de Bayes]], [[Suposición de Markov]]

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - pág. 3 → Los tres axiomas
  - pág. 4 → Detalle del Axioma 3