---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Robótica Probabilística]] | [[Home|← Inicio]]

# Actualización Recursiva Bayesiana

> Cómo combinar varias observaciones $z_1, \dots, z_n$ aplicando Bayes paso a paso, asumiendo que cada observación es independiente de las anteriores dado el estado.

## Prerequisitos
- [[Regla de Bayes]] — base teórica.
- [[Probabilidad - Axiomas y Variables Aleatorias]] — independencia condicional.

## 1. El problema  *(Teóricas 05-intro_robo_proba parte 1, slide 20)*
El robot ya estimó $P(\text{abierta} \mid z_1)$ con la primera medición. Ahora obtiene una segunda medición $z_2$. ¿Cómo incorporamos esta nueva información?

En general, **¿cómo estimamos $P(x \mid z_1, \dots, z_n)$?**

## 2. Aplicación recursiva de Bayes con suposición de Markov  *(Teóricas 05-intro_robo_proba parte 1, slide 21)*
Aplicando Bayes con conocimiento de fondo $z_1, \dots, z_{n-1}$:

$$P(x \mid z_1, \dots, z_n) = \frac{P(z_n \mid x, z_1, \dots, z_{n-1})\,P(x \mid z_1, \dots, z_{n-1})}{P(z_n \mid z_1, \dots, z_{n-1})}$$

> [!info] Suposición de Markov sobre observaciones
> $z_n$ es **independiente** de $z_1, \dots, z_{n-1}$ una vez que conocemos el estado $x$. Es decir: el estado es **suficiente** — todas las mediciones pasadas ya quedaron explicadas por él.

Aplicando esa suposición:

$$P(x \mid z_1, \dots, z_n) = \frac{P(z_n \mid x)\,P(x \mid z_1, \dots, z_{n-1})}{P(z_n \mid z_1, \dots, z_{n-1})} = \eta\,P(z_n \mid x)\,P(x \mid z_1, \dots, z_{n-1})$$

E iterando hacia atrás, hasta llegar al prior $P(x)$:

$$P(x \mid z_1, \dots, z_n) = \eta_{1\dots n} \left[\prod_{i=1\dots n} P(z_i \mid x)\right] P(x)$$

![[Bayes - recursiva derivacion.png]]
*Derivación de la actualización recursiva con suposición de Markov, slide 21.*

> [!warning] Idea clave
> El producto de likelihoods $\prod P(z_i \mid x)$ acumula evidencia sobre cada hipótesis $x$, ponderada por el prior. La normalización $\eta$ se aplica al final.

## 3. Ejemplo: segunda medición de la puerta  *(Teóricas 05-intro_robo_proba parte 1, slide 22)*
Continuamos con el ejemplo anterior. El robot ya tenía $P(\text{abierta} \mid z_1) = 2/3$. Ahora obtiene una segunda medición $z_2$ con likelihood:

- $P(z_2 \mid \text{abierta}) = 0.25$
- $P(z_2 \mid \neg\text{abierta}) = 0.3$

Aplicando la actualización recursiva:

$$P(\text{abierta} \mid z_2, z_1) = \frac{P(z_2 \mid \text{abierta})\,P(\text{abierta} \mid z_1)}{P(z_2 \mid \text{abierta})\,P(\text{abierta} \mid z_1) + P(z_2 \mid \neg\text{abierta})\,P(\neg\text{abierta} \mid z_1)}$$

$$= \frac{\tfrac{1}{4}\cdot\tfrac{2}{3}}{\tfrac{1}{4}\cdot\tfrac{2}{3} + \tfrac{3}{10}\cdot\tfrac{1}{3}} = \frac{\tfrac{1}{6}}{\tfrac{1}{6} + \tfrac{1}{10}} = \frac{5}{8} = 0.625$$

Esta vez, $z_2$ **disminuye** la probabilidad de que la puerta esté abierta (porque el sensor es más sensible cuando está cerrada).

![[Bayes - segunda medicion.png]]
*Cálculo de la actualización con $z_2$ — la creencia baja de 0.67 a 0.625, slide 22.*

## 4. Lectura intuitiva
- Cada medición **multiplica** el belief actual por su likelihood.
- La **normalización** asegura que la distribución resultante sigue sumando 1.
- La suposición de Markov es lo que hace que esto sea **eficiente** — sólo necesitamos guardar el belief actual, no toda la historia de mediciones.

## Variantes y conexiones
- [[Modelado de Acciones]] — la otra mitad: cómo incorporar acciones, no sólo observaciones.
- [[Filtro de Bayes]] — combina ambas en un proceso recursivo completo.
- [[Modelo de Sensor Basado en Haz]] — una de las formas de obtener $P(z_i \mid x)$.

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` — slides 20–22.
