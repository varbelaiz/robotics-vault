---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Robótica Probabilística]] | [[Home|← Inicio]]

# Filtro de Bayes

> Algoritmo recursivo que mantiene una distribución de probabilidad (**belief**) sobre el estado de un sistema dinámico, alternando una etapa de **predicción** (incorporar la acción $u_t$) y una de **corrección** (incorporar la observación $z_t$).

## Prerequisitos
- [[Regla de Bayes]]
- [[Actualización Recursiva Bayesiana]]
- [[Modelado de Acciones]]
- [[Probabilidad - Axiomas y Variables Aleatorias]] — marginalización, ley de probabilidad total.

## 1. Estructura del filtro de Bayes  *(Teóricas 05-intro_robo_proba parte 2, slide 31)*
**Dado**:
- Una serie de observaciones $z$ y acciones $u$: $d_t = \{u_1, z_1, \dots, u_t, z_t\}$.
- **Modelo del sensor**: $P(z \mid x)$.
- **Modelo de acción**: $P(x \mid u, x')$.
- **Probabilidad a priori** del estado: $P(x)$.

**Se desea**: estimar el estado $X$ del sistema dinámico, es decir la **probabilidad a posteriori** del estado, también llamada **belief**:

$$\text{Bel}(x_t) = P(x_t \mid u_1, z_1, \dots, u_t, z_t)$$

![[Filtro Bayes - estructura.png]]
*Inputs y output del filtro de Bayes, slide 31.*

## 2. Suposición de Markov  *(Teóricas 05-intro_robo_proba parte 2, slide 32)*
La suposición central que hace tratable el filtro: **el estado actual es suficiente** para predecir el siguiente y para explicar la observación. Formalmente:

$$P(z_t \mid x_{0:t}, z_{1:t-1}, u_{1:t}) = P(z_t \mid x_t)$$
$$P(x_t \mid x_{1:t-1}, z_{1:t-1}, u_{1:t}) = P(x_t \mid x_{t-1}, u_t)$$

![[Markov - DBN.png]]
*Red Bayesiana dinámica: cada $x_t$ depende sólo de $x_{t-1}$ y $u_t$; cada $z_t$ depende sólo de $x_t$, slide 32.*

> [!warning] Suposiciones subyacentes
> - **Mundo estático** (los obstáculos no se mueven entre pasos).
> - **Ruido independiente** entre pasos.
> - **Modelo perfecto**, sin errores de aproximación.
>
> En la práctica estas suposiciones se violan, pero el filtro funciona bien igual gracias a la ponderación probabilística.

## 3. Derivación paso a paso  *(Teóricas 05-intro_robo_proba parte 2, slides 34–40)*
Partimos de la definición del belief:

$$\text{bel}(x_t) = p(x_t \mid z_{1:t}, u_{1:t})$$

**Paso 1 — Bayes** (separar la última observación):
$$= \eta\,p(z_t \mid x_t, z_{1:t-1}, u_{1:t})\,p(x_t \mid z_{1:t-1}, u_{1:t})$$

**Paso 2 — Markov** (la observación sólo depende del estado actual):
$$= \eta\,p(z_t \mid x_t)\,p(x_t \mid z_{1:t-1}, u_{1:t})$$

**Paso 3 — Probabilidad total** (marginalizar sobre el estado previo):
$$= \eta\,p(z_t \mid x_t)\int p(x_t \mid x_{t-1}, z_{1:t-1}, u_{1:t})\,p(x_{t-1} \mid z_{1:t-1}, u_{1:t})\,dx_{t-1}$$

**Paso 4 — Markov** (la transición sólo depende del estado previo y la acción):
$$= \eta\,p(z_t \mid x_t)\int p(x_t \mid x_{t-1}, u_t)\,p(x_{t-1} \mid z_{1:t-1}, u_{1:t})\,dx_{t-1}$$

**Paso 5 — Independencia** (el estado previo no depende de la acción actual):
$$= \eta\,p(z_t \mid x_t)\int p(x_t \mid x_{t-1}, u_t)\,p(x_{t-1} \mid z_{1:t-1}, u_{1:t-1})\,dx_{t-1}$$

**Paso 6 — Reconocer el término recursivo** ($\text{bel}(x_{t-1})$):

$$= \eta\,p(z_t \mid x_t)\int p(x_t \mid x_{t-1}, u_t)\,\text{bel}(x_{t-1})\,dx_{t-1}$$

![[Filtro Bayes - derivacion final.png]]
*Las seis líneas de la derivación con la observación recursiva final, slide 40.*

## 4. La fórmula maestra
$$\boxed{\text{Bel}(x_t) = \eta\,P(z_t \mid x_t)\int P(x_t \mid u_t, x_{t-1})\,\text{Bel}(x_{t-1})\,dx_{t-1}}$$

Dos partes:
- **Predicción** (integral): propaga el belief anterior a través del modelo de acción.
- **Corrección** (multiplicación por likelihood + normalización): pondera por qué tan compatible es la observación con cada estado.

## 5. Algoritmo  *(Teóricas 05-intro_robo_proba parte 2, slide 41)*
```
Filtro_de_Bayes(Bel(x), d):
  η = 0
  if d is a perceptual data item z then
    for all x do
      Bel'(x) = P(z | x) · Bel(x)
      η = η + Bel'(x)
    for all x do
      Bel'(x) = η⁻¹ · Bel'(x)
  else if d is an action data item u then
    for all x do
      Bel'(x) = ∫ P(x | u, x') · Bel(x') dx'
  return Bel'(x)
```

![[Filtro Bayes - algoritmo.png]]
*Algoritmo `Filtro_de_Bayes` en pseudocódigo, slide 41.*

> [!info] Dos puertas de entrada
> El algoritmo separa los dos tipos de input. Cada vez que llega una observación, **corrige**. Cada vez que llega una acción, **predice**. La alternancia es lo que mantiene el belief actualizado en tiempo real.

## 6. Casos particulares — los hijos del filtro  *(Teóricas 05-intro_robo_proba parte 2, slide 42)*
La misma fórmula instancia múltiples algoritmos clásicos según cómo se represente el belief y los modelos:

- [[Filtro de Kalman]] — belief gaussiano + modelos lineales (forward-ref, M5).
- [[MCL - Filtro de Partículas|Filtros de Partículas]] — belief representado por muestras (forward-ref, M5).
- [[Filtros Discretos|Modelos ocultos de Markov]] — estado discreto (forward-ref, M5).
- **Redes Bayesianas dinámicas** — generalización con múltiples variables.
- **POMDPs** (Procesos de decisión de Markov parcialmente observables) — incluye decisión de acciones.

## 7. Ejemplo: localización probabilística  *(Teóricas 05-intro_robo_proba parte 2, slide 43)*
Un robot en un pasillo con tres puertas. El sensor detecta puertas, pero no las distingue entre sí.

1. **Inicio**: belief uniforme — el robot puede estar en cualquier parte.
2. **Observa una puerta**: el belief desarrolla tres picos, uno frente a cada puerta.
3. **Se mueve**: la acción propaga (y "ensancha") los picos, que se desplazan junto con el robot.
4. **Observa otra puerta**: la corrección refuerza un pico (donde la observación es consistente con el movimiento esperado) y atenúa los otros.

![[Localizacion probabilistica - pasos.png]]
*Cuatro pasos del filtro de Bayes en localización 1D: inicio uniforme → primera observación (3 picos) → movimiento (picos desplazados) → segunda observación (un pico domina), slide 43.*

> [!info] Por qué el filtro converge
> Cada par observación-movimiento agrega información que es inconsistente con todas las hipótesis salvo unas pocas. El producto de likelihoods las atenúa progresivamente, hasta que un único modo domina.

## 8. Resumen  *(Teóricas 05-intro_robo_proba parte 2, slide 45)*
- La regla de **Bayes** nos permite calcular probabilidades que de otra forma serían muy complejas.
- Con la suposición de **Markov**, la actualización recursiva Bayesiana es una forma **eficiente** de combinar evidencia.
- Los **filtros de Bayes** son la herramienta probabilística para estimar el estado de un sistema dinámico — y todos los filtros del módulo 5 son casos particulares de éste.

## Variantes y conexiones
- [[Modelado de Acciones]] — la mitad de "predicción".
- [[Actualización Recursiva Bayesiana]] — la mitad de "corrección".
- [[Modelo de Sensor Basado en Haz]] — instancia concreta de $P(z \mid x)$.
- [[Modelo de Movimiento (Odometría)|Modelo de Movimiento (Odometría)]] — instancia concreta de $P(x \mid u, x')$.
- [[Filtro de Kalman]] / [[MCL - Filtro de Partículas]] / [[Filtros Discretos]] — implementaciones específicas (forward-refs, M5).

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` — slide 31 (estructura).
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf` — slides 32–45.
