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

$P(A)$ indica la probabilidad de que la proposición $A$ sea verdadera.

1. **No negatividad**: $0 \leq P(A) \leq 1$
2. **Normalización**: $P(\text{True}) = 1 \quad P(\text{False}) = 0$
3. **Aditividad**: $P(A \lor B) = P(A) + P(B) - P(A \land B)$

![[Probabilidad - axiomas.png]]
*Los tres axiomas de Kolmogorov, slide 3.*

## Detalle del Axioma 3

![[Probabilidad - axioma 3 Venn.png]]
*Visualización del axioma 3 con un diagrama de Venn, slide 4.*

Si sumamos $P(A) + P(B)$ contamos dos veces la intersección, por eso restamos $P(A \land B)$.

Si $A$ y $B$ son **mutuamente excluyentes** ($A \land B = \text{False}$), entonces $P(A \lor B) = P(A) + P(B)$.

> [!info] Derivación útil
> Aplicando el axioma 3 a $A$ y $\neg A$: $P(\text{True}) = P(A) + P(\neg A) - P(\text{False})$, de donde $P(\neg A) = 1 - P(A)$.

## Consecuencias

- $P(\text{False}) = 0$
- $P(\neg A) = 1 - P(A)$
- Si $A \implies B$, entonces $P(A) \leq P(B)$

## Conexiones
- Base para todas las reglas que siguen: [[Probabilidad Condicional]], [[Regla de Bayes]], [[Suposición de Markov]]

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - págs. 3, 5 → Los tres axiomas
  - pág. 4 → Detalle del Axioma 3