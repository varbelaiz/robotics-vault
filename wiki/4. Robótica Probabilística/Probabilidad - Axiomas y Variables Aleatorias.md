---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Robótica Probabilística]] | [[Home|← Inicio]]

# Probabilidad - Axiomas y Variables Aleatorias

> Repaso del lenguaje base que usaremos para todo el módulo: axiomas, variables aleatorias, conjunta, condicional, marginalización.

## Prerequisitos
- *(ninguno — entrada al módulo)*

## 1. Idea principal de la robótica probabilística

> **Representación explícita de la incertidumbre** usando la teoría del cálculo de probabilidades.
>
> Tratemos de saber lo que no sabemos.

Toda observación es ruidosa, todo movimiento incierto, todo modelo aproximado. En vez de pretender que sabemos el estado exacto, mantenemos una **distribución de probabilidad** sobre los estados posibles. La incertidumbre deja de ser un bug y se vuelve una variable de primera clase.

## 2. Axiomas de la probabilidad

$P(A)$ indica la probabilidad de que la proposición $A$ sea verdadera.

1. $0 \le P(A) \le 1$
2. $P(\text{True}) = 1 \qquad P(\text{False}) = 0$
3. $P(A \lor B) = P(A) + P(B) - P(A \land B)$

![[Probabilidad - axiomas.png]]
*Los tres axiomas de Kolmogorov, slide 3.*

El **axioma 3** se ve clarísimo con un diagrama de Venn: si sumamos $P(A) + P(B)$ contamos dos veces la intersección, por eso restamos $P(A \land B)$.

![[Probabilidad - axioma 3 Venn.png]]
*Visualización del axioma 3 con un diagrama de Venn, slide 4.*

> [!info] Derivación útil
> Aplicando el axioma 3 a $A$ y $\neg A$: $P(\text{True}) = P(A) + P(\neg A) - P(\text{False})$, de donde $P(\neg A) = 1 - P(A)$.

## 3. Variables aleatorias discretas

- $X$ denota una **variable aleatoria**.
- $X$ puede tomar un número contable de valores en $\{x_1, x_2, \dots, x_n\}$.
- $P(X = x_i)$ o $P(x_i)$ es la probabilidad de que $X$ tome el valor $x_i$.
- $P(\cdot)$ es la **función de masa de probabilidad**.

Ejemplo: $P(\text{habitación}) = \langle 0.7, 0.2, 0.08, 0.02\rangle$.

## 4. Variables aleatorias continuas

- $X$ toma valores en un continuo.
- $p(X = x)$ o $p(x)$ es una **función de densidad de probabilidad** (PDF).
- La probabilidad de que $X$ caiga en un intervalo es la integral de la densidad:

$$P(x \in [a, b]) = \int_a^b p(x)\,dx$$

![[Variables aleatorias - continua ejemplo.png]]
*Dos densidades de probabilidad continuas (multimodales), slide 7.*

## 5. Las probabilidades suman 1

| Caso discreto | Caso continuo |
|---|---|
| $\sum_x P(x) = 1$ | $\int p(x)\,dx = 1$ |

## 6. Probabilidad conjunta e independencia

$$P(X = x \text{ e } Y = y) = P(x, y)$$

Si $X$ e $Y$ son **independientes**:
$$P(x, y) = P(x)\,P(y)$$

## 7. Probabilidad condicional

$P(x \mid y)$ es la probabilidad de $x$ **dado** $y$:

$$P(x \mid y) = \frac{P(x, y)}{P(y)}$$
$$P(x, y) = P(x \mid y)\,P(y)$$

Si $X$ e $Y$ son independientes, entonces $P(x \mid y) = P(x)$ — saber $y$ no aporta nada.

## 8. Ley de la probabilidad total

Para "des-condicionar" sumando sobre todos los valores posibles de $y$:

| Caso discreto | Caso continuo |
|---|---|
| $P(x) = \sum_y P(x \mid y)\,P(y)$ | $p(x) = \int p(x \mid y)\,p(y)\,dy$ |

Conocida también como la fórmula de **expansión por casos**.

## 9. Marginalización

Para obtener la distribución de una variable a partir de la conjunta, **sumar/integrar sobre la otra**:

| Caso discreto | Caso continuo |
|---|---|
| $P(x) = \sum_y P(x, y)$ | $p(x) = \int p(x, y)\,dy$ |

> [!info] Marginalización vs probabilidad total
> Son **la misma idea**: la probabilidad total se obtiene marginalizando $P(x, y)$ y reescribiendo $P(x, y) = P(x \mid y) P(y)$.

## Variantes y conexiones
- [[Regla de Bayes]] — la herramienta central que se construye con estas piezas.
- [[Filtro de Bayes]] — destino final del módulo.

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - slide 2 → 1. Idea principal de la robótica probabilística
  - slides 3–5 → 2. Axiomas de la probabilidad
  - slide 6 → 3. Variables aleatorias discretas
  - slide 7 → 4. Variables aleatorias continuas
  - slide 8 → 5. Las probabilidades suman 1
  - slide 9 → 6. Probabilidad conjunta e independencia
  - slide 10 → 7. Probabilidad condicional
  - slide 11 → 8. Ley de la probabilidad total
  - slide 12 → 9. Marginalización