---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Robótica Probabilística]] | [[Home|← Inicio]]

# Modelado de Acciones

> Cómo incorporar las acciones $u$ del robot al belief actual: a diferencia de las mediciones (que **reducen** la incertidumbre), las acciones en general la **incrementan**.

## Prerequisitos
- [[Regla de Bayes]] — base.
- [[Actualización Recursiva Bayesiana]] — la mitad de "observaciones" del filtro.

## 1. Acciones y dinamismo

En general el mundo es **dinámico** debido a:
- **Acciones realizadas por el robot** (girar las ruedas, mover el manipulador).
- **Acciones realizadas por otros agentes**.
- O el simple **paso del tiempo** que produce cambios en el mundo.

Necesitamos una forma de incorporar estos cambios a nuestro belief.

## 2. Acciones más comunes

- El robot **gira sus ruedas** para moverse.
- El robot **usa su manipulador** para agarrar un objeto.
- El **entorno cambia con el tiempo** (puertas que se abren/cierran, gente que se mueve).

> [!warning] Las acciones nunca son perfectas
> En contraste con las mediciones, las **acciones en general incrementan la incerteza**. Aunque el robot mande "moverse 1 metro adelante", el resultado real depende del piso, las ruedas, etc. Por eso las modelamos probabilísticamente.

## 3. El modelo $P(x \mid u, x')$

Para incorporar el resultado de una acción $u$ al belief actual usamos la **función de densidad de probabilidad condicional**:

$$\boxed{P(x \mid u, x')}$$

Este término especifica la densidad de probabilidad según la cual, **ejecutando $u$, el estado cambia de $x'$ a $x$**.

![[Acciones - modelo Pxux.png]]
*Notación del modelo de transición $P(x \mid u, x')$, slide 25.*

> [!info] Notación
> Algunos textos usan $x_t = x$ y $x_{t-1} = x'$. Es la misma idea: el estado **previo** está primado, el **nuevo** sin primar.

## 4. Ejemplo: cerrar una puerta

El robot ejecuta la acción $u = $ "cerrar puerta" pulsando con su brazo:

![[Acciones - cerrar puerta.png]]
*Robot ejecutando la acción de cerrar la puerta, slide 26.*

El modelo de transición $P(x \mid u, x')$ es una **máquina de estados finitos** con probabilidades en las aristas:

| $x'$ (antes) | $x$ (después) | $P(x \mid u, x')$ |
|---|---|---|
| abierta | cerrada | 0.9 |
| abierta | abierta | 0.1 |
| cerrada | cerrada | 1 |
| cerrada | abierta | 0 |

Si la puerta está abierta, la acción "cerrar puerta" tiene **éxito el 90% de las veces**.

![[Acciones - transiciones FSM.png]]
*Diagrama de transiciones de estado para la acción "cerrar puerta", slide 27.*

## 5. Integrando el resultado de las acciones

Para obtener el belief $P(x \mid u)$ después de la acción, **marginalizamos sobre el estado anterior** $x'$:

| Caso continuo | Caso discreto |
|---|---|
| $P(x \mid u) = \int P(x \mid u, x')\,P(x')\,dx'$ | $P(x \mid u) = \sum_{x'} P(x \mid u, x')\,P(x')$ |

![[Acciones - integrando formula.png]]
*Integración del modelo de transición sobre el estado anterior, slide 28.*

> [!info] Suposición de independencia
> Asumimos que $P(x' \mid u) = P(x')$, es decir: el estado anterior no depende de la acción que estamos a punto de ejecutar. Esto permite eliminar la $u$ del segundo factor.

## 6. Ejemplo del belief resultante

Continuando con el ejemplo de la puerta, supongamos que el belief antes de la acción es $P(\text{abierta}) = 5/8$, $P(\text{cerrada}) = 3/8$.

$$P(\text{cerrada} \mid u) = P(\text{cerrada} \mid u, \text{abierta})\,P(\text{abierta}) + P(\text{cerrada} \mid u, \text{cerrada})\,P(\text{cerrada})$$

$$= \frac{9}{10} \cdot \frac{5}{8} + \frac{1}{1} \cdot \frac{3}{8} = \frac{15}{16}$$

Análogamente, $P(\text{abierta} \mid u) = 1/16$.

![[Acciones - belief ejemplo.png]]
*Cálculo del belief resultante después de la acción "cerrar puerta", slide 30.*

> [!warning] Después de la acción, todavía hay incertidumbre
> Aunque la acción "cerrar puerta" es muy buena (90% de éxito), el belief final no es 100% cerrada — porque arrastramos la incertidumbre del belief previo. Esto es típico: las acciones nunca eliminan la incerteza, sólo la propagan.

## 7. Anticipo: la estructura del filtro de Bayes

Con esto tenemos los dos ingredientes:
- **Modelo del sensor**: $P(z \mid x)$ — cómo las observaciones depende del estado.
- **Modelo de acción**: $P(x \mid u, x')$ — cómo el estado evoluciona con las acciones.
- **Probabilidad a priori** del estado: $P(x)$.

El **filtro de Bayes** es el algoritmo que combina los tres recursivamente para estimar $\text{Bel}(x_t) = P(x_t \mid u_1, z_1, \dots, u_t, z_t)$.

![[Filtro Bayes - estructura.png]]
*Estructura del filtro de Bayes: dato + sensor + acción + prior → belief, slide 31.*

## Variantes y conexiones
- [[Filtro de Bayes]] — combina observaciones y acciones recursivamente.
- [[Modelo de Movimiento (Odometría)|Modelo de Movimiento (Odometría)]] — instancia concreta de $P(x \mid u, x')$ basada en encoders.
- [[Modelo de Movimiento (Velocidad)]] — instancia concreta basada en velocidades.
- [[Actualización Recursiva Bayesiana]] — la otra mitad (observaciones).

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - slide 23 → 1. Acciones y dinamismo
  - slide 24 → 2. Acciones más comunes
  - slide 25 → 3. El modelo $P(x \mid u, x')$
  - slides 26–27 → 4. Ejemplo: cerrar una puerta
  - slide 28 → 5. Integrando el resultado de las acciones
  - slides 29–30 → 6. Ejemplo del belief resultante
  - slide 31 → 7. Anticipo: la estructura del filtro de Bayes