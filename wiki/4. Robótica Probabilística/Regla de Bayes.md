---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Robótica Probabilística]] | [[Home|← Inicio]]

# Regla de Bayes

> Cómo invertir una probabilidad condicional para pasar de información **causal** ($P(z \mid x)$, "qué medición espero dado el estado") a información **diagnóstica** ($P(x \mid z)$, "qué estado infiero dada la medición").

## Prerequisitos
- [[Probabilidad - Axiomas y Variables Aleatorias]] — necesitamos conjunta, condicional y la noción de marginalización.

## 1. Derivación de la fórmula  *(Teóricas 05-intro_robo_proba parte 1, slide 13)*
Partimos de la igualdad de la conjunta expresada de dos maneras:

$$P(x, y) = P(x \mid y)\,P(y) = P(y \mid x)\,P(x)$$

Despejando $P(x \mid y)$:

$$\boxed{P(x \mid y) = \frac{P(y \mid x)\,P(x)}{P(y)} = \frac{\text{likelihood} \cdot \text{prior}}{\text{evidence}}}$$

![[Bayes - formula.png]]
*Fórmula canónica de Bayes con la nomenclatura prior/likelihood/evidence, slide 13.*

| Término | Nombre | Qué representa |
|---|---|---|
| $P(x)$ | **Prior** | Lo que creíamos antes de ver $y$. |
| $P(y \mid x)$ | **Likelihood** (verosimilitud) | Qué esperamos observar dado $x$. |
| $P(y)$ | **Evidence** | Constante de normalización. |
| $P(x \mid y)$ | **Posterior** | Lo que creemos después de ver $y$. |

## 2. Normalización  *(Teóricas 05-intro_robo_proba parte 1, slide 14)*
La evidence $P(y)$ no depende de $x$, por lo que se la trata como **constante de normalización** $\eta$:

$$P(x \mid y) = \eta\,P(y \mid x)\,P(x), \qquad \eta = P(y)^{-1} = \frac{1}{\sum_x P(y \mid x)\,P(x)}$$

```
∀x: aux_{x|y} = P(y | x) · P(x)
η = 1 / Σ_x aux_{x|y}
∀x: P(x | y) = η · aux_{x|y}
```

![[Bayes - normalizacion.png]]
*Algoritmo en dos pasos: calcular auxiliares, luego normalizar, slide 14.*

> [!info] Por qué importa esto
> En la práctica casi nunca calculamos $P(y)$ explícitamente. Computamos la cantidad no normalizada $P(y \mid x) P(x)$ para todos los valores de $x$, y al final dividimos por la suma. Esto es exactamente lo que hace todo filtro de Bayes.

## 3. Bayes con conocimiento de fondo  *(Teóricas 05-intro_robo_proba parte 1, slide 15)*
Si todo está condicionado a un conocimiento $z$ (e.g. el mapa, la calibración del sensor):

$$P(x \mid y, z) = \frac{P(y \mid x, z)\,P(x \mid z)}{P(y \mid z)}$$

Misma estructura, simplemente arrastrando $z$ en cada término.

## 4. Independencia condicional  *(Teóricas 05-intro_robo_proba parte 1, slide 16)*
$$P(x, y \mid z) = P(x \mid z)\,P(y \mid z)$$

Equivalentemente:
- $P(x \mid z) = P(x \mid z, y)$
- $P(y \mid z) = P(y \mid z, x)$

> [!warning] No implica independencia marginal
> $P(x, y \mid z) = P(x \mid z) P(y \mid z)$ **no** implica $P(x, y) = P(x) P(y)$. Dos variables pueden ser dependientes globalmente pero independientes una vez que conocemos un tercer factor.

## 5. Ejemplo: ¿está abierta la puerta?  *(Teóricas 05-intro_robo_proba parte 1, slides 17–19)*
Un robot apunta su sensor a una puerta y obtiene la medición $z$. ¿Cuánto vale $P(\text{abierta} \mid z)$?

![[Bayes - puerta abierta.png]]
*Robot midiendo una puerta con su sensor, slide 17.*

**Causal vs diagnóstico**:
- $P(\text{abierta} \mid z)$ es información **diagnóstica** (lo que queremos).
- $P(z \mid \text{abierta})$ es información **causal** (más fácil de obtener — basta con contar frecuencias).

Bayes nos permite ir de la causal a la diagnóstica.

![[Bayes - causal vs diagnostico.png]]
*Razonamiento causal vs diagnóstico — Bayes hace el puente, slide 18.*

**Ejemplo numérico**: con $P(z \mid \text{abierta}) = 0.6$, $P(z \mid \neg\text{abierta}) = 0.3$ y $P(\text{abierta}) = P(\neg\text{abierta}) = 0.5$:

$$P(\text{abierta} \mid z) = \frac{P(z \mid \text{abierta})\,P(\text{abierta})}{P(z \mid \text{abierta})\,P(\text{abierta}) + P(z \mid \neg\text{abierta})\,P(\neg\text{abierta})}$$

$$= \frac{0.6 \cdot 0.5}{0.6 \cdot 0.5 + 0.3 \cdot 0.5} = \frac{0.3}{0.45} = \frac{2}{3} \approx 0.67$$

La medición $z$ **eleva** la probabilidad de que la puerta esté abierta de $0.5$ a $0.67$.

![[Bayes - ejemplo numerico puerta.png]]
*Cálculo numérico completo del ejemplo de la puerta, slide 19.*

## Variantes y conexiones
- [[Probabilidad - Axiomas y Variables Aleatorias]] — conceptos previos.
- [[Actualización Recursiva Bayesiana]] — cómo extender a múltiples mediciones.
- [[Modelado de Acciones]] — el otro ingrediente del filtro de Bayes.
- [[Filtro de Bayes]] — Bayes aplicado recursivamente con observaciones y acciones.

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` — slides 13–19.
