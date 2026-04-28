---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
  - Raw/Libro/ProbabilisticRobotics.pdf
ultima_actualizacion: 2026-04-28
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Regla de Bayes

> Actualización de creencias al recibir nueva evidencia. El corazón de la robótica probabilística.

## Fórmula

![[Bayes - formula.png]]
*La fórmula de Bayes: convierte conocimiento causal en diagnóstico, slide 13.*

$$P(x \mid z) = \frac{P(z \mid x) P(x)}{P(z)}$$

Donde:
- $P(x)$: **prior** — creencia previa sobre el estado
- $P(z \mid x)$: **verosimilitud** — modelo de sensor, probabilidad de la medición dado el estado
- $P(x \mid z)$: **posterior** — creencia actualizada
- $P(z)$: **evidencia** — factor de normalización

> [!info] Modelo generativo (Thrun et al., §2.2)
> En robótica, $P(z \mid x)$ se conoce como **modelo generativo**: describe cómo el estado $X$ "genera" o causa la medición $Z$. Esta dirección es la fácil de modelar — el modelo del lidar dice qué scan se espera dada una pose. Bayes es lo que invierte ese modelo causal en uno **diagnóstico** ($P(x \mid z)$), que es lo que el robot necesita para inferir su estado a partir de las mediciones.

## Normalización

![[Bayes - normalizacion.png]]
*El factor de normalización $\eta = 1/P(z)$ asegura que la posterior sume 1, slide 14.*

La evidencia se calcula como:

- **Discreto**: $P(z) = \sum_{x} P(z \mid x) P(x)$
- **Continuo**: $p(z) = \int p(z \mid x) p(x) \, dx$

En la práctica, se calcula $\eta$ para que la distribución resultante esté normalizada.

## Bayes con conocimiento de fondo

Cuando hay variables adicionales (conocimiento de fondo $b$):

$$P(x \mid z, b) = \frac{P(z \mid x, b) P(x \mid b)}{P(z \mid b)}$$

## Independencia condicional

Si $X$ e $Y$ son incondicionalmente dependientes, pueden ser **condicionalmente independientes** dado $Z$:

$$P(x \mid y, z) = P(x \mid z)$$

Esto no implica que $P(y \mid x, z) = P(y \mid z)$ ni que $P(x, y \mid z) = P(x \mid z) P(y \mid z)$ automáticamente.

> [!warning] Independencia condicional ≠ independencia absoluta (Thrun et al., §2.2)
> Las dos son **lógicamente independientes**: ninguna implica la otra.
> - $P(x, y \mid z) = P(x \mid z)\,P(y \mid z)$ **NO implica** $P(x, y) = P(x)\,P(y)$.
> - Y al revés: independencia absoluta no garantiza independencia condicional.
>
> En robótica esto importa porque dos mediciones consecutivas $z_1, z_2$ son **condicionalmente independientes dado el estado** $x$ — pero no son independientes en absoluto, ya que ambas dependen del mismo $x$ desconocido. Por eso la verosimilitud combinada se multiplica como $P(z_1 \mid x)\,P(z_2 \mid x)$ una vez que $x$ está dado, pero $P(z_1, z_2)$ no factoriza.

## Actualización recursiva: combinando evidencia

¿Cómo incorporar múltiples mediciones $z_1, z_2, \ldots, z_n$? Aplicando Bayes con conocimiento de fondo $z_1, \dots, z_{n-1}$:

$$P(x \mid z_1, \dots, z_n) = \frac{P(z_n \mid x, z_1, \dots, z_{n-1})\,P(x \mid z_1, \dots, z_{n-1})}{P(z_n \mid z_1, \dots, z_{n-1})}$$

> [!info] Suposición de Markov sobre observaciones
> $z_n$ es **independiente** de $z_1, \dots, z_{n-1}$ una vez que conocemos el estado $x$. El estado es **suficiente** — toda la información de mediciones pasadas ya quedó capturada en él.

Aplicando esa suposición:

$$P(x \mid z_1, \dots, z_n) = \eta\,P(z_n \mid x)\,P(x \mid z_1, \dots, z_{n-1})$$

E iterando hacia atrás hasta llegar al prior $P(x)$:

$$P(x \mid z_1, \dots, z_n) = \eta_{1\dots n} \left[\prod_{i=1\dots n} P(z_i \mid x)\right] P(x)$$

![[Bayes - recursiva derivacion.png]]
*Derivación de la actualización recursiva con suposición de Markov, slide 21.*

> [!warning] Idea clave
> El producto de likelihoods $\prod P(z_i \mid x)$ acumula evidencia sobre cada hipótesis $x$, ponderada por el prior. La normalización $\eta$ se aplica al final. Sólo necesitamos guardar el **belief actual**, no toda la historia de mediciones — la suposición de Markov es lo que hace eficiente al filtro.

### Ejemplo: segunda medición de la puerta

Continuamos con el ejemplo anterior: el robot ya tenía $P(\text{abierta} \mid z_1) = 2/3$. Ahora obtiene una segunda medición $z_2$ con likelihood:

- $P(z_2 \mid \text{abierta}) = 0.25$
- $P(z_2 \mid \neg\text{abierta}) = 0.3$

Aplicando la actualización recursiva:

$$P(\text{abierta} \mid z_2, z_1) = \frac{P(z_2 \mid \text{abierta})\,P(\text{abierta} \mid z_1)}{P(z_2 \mid \text{abierta})\,P(\text{abierta} \mid z_1) + P(z_2 \mid \neg\text{abierta})\,P(\neg\text{abierta} \mid z_1)}$$

$$= \frac{\tfrac{1}{4}\cdot\tfrac{2}{3}}{\tfrac{1}{4}\cdot\tfrac{2}{3} + \tfrac{3}{10}\cdot\tfrac{1}{3}} = \frac{\tfrac{1}{6}}{\tfrac{1}{6} + \tfrac{1}{10}} = \frac{5}{8} = 0.625$$

![[Bayes - segunda medicion.png]]
*La creencia baja de $0.67$ a $0.625$: $z_2$ es más sensible cuando la puerta está cerrada, slide 22.*

Esta vez, $z_2$ **disminuye** la probabilidad de que la puerta esté abierta.

## Conexiones
- [[Filtro de Bayes]] — aplica Bayes recursivamente con acciones entre mediciones
- [[Suposición de Markov]] — permite la actualización recursiva
- [[Tutorial 5 - Bayes]] — ejercicios prácticos
- [[Filtros Discretos]] — implementación en espacio discreto

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - pág. 13 → Fórmula
  - pág. 14 → Normalización
  - pág. 15 → Bayes con conocimiento de fondo
  - pág. 16 → Independencia condicional
  - págs. 20–22 → Actualización recursiva: combinando evidencia
- `Raw/Libro/ProbabilisticRobotics.pdf`
  - pág. 13 → Modelo generativo / inverso (§2.2)
  - pág. 15 → Independencia condicional vs absoluta (§2.2)