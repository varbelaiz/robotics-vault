---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-27
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Modelo de Movimiento

> Cómo incorporar el resultado de una acción $u$ al belief actual: $P(x_t \mid u_t, x_{t-1})$. A diferencia de las mediciones (que **reducen** la incertidumbre), las acciones en general la **incrementan**.

## 1. Acciones y dinamismo

El mundo es **dinámico** debido a:

1. **Acciones del robot** — girar las ruedas para moverse, usar el manipulador para agarrar.
2. **Acciones de otros agentes** — personas, otros robots, puertas que se abren/cierran.
3. **Paso del tiempo** — cambios naturales del entorno.

Necesitamos una forma de incorporar estos cambios al belief.

> [!warning] Las acciones nunca son perfectas
> En contraste con las mediciones, las **acciones en general incrementan la incerteza**. Aunque el robot mande "moverse 1 metro adelante", el resultado real depende del piso, las ruedas, el deslizamiento. Por eso las modelamos probabilísticamente.

## 2. El modelo $P(x \mid u, x')$

Para incorporar el resultado de una acción $u$ al belief actual usamos la **función de densidad de probabilidad condicional**:

$$\boxed{P(x \mid u, x')}$$

Este término especifica la densidad según la cual, **ejecutando $u$, el estado cambia de $x'$ a $x$**.

![[Acciones - modelo Pxux.png]]
*Notación del modelo de transición $P(x \mid u, x')$, slide 25.*

> [!info] Notación
> Algunos textos usan $x_t = x$ y $x_{t-1} = x'$. Es la misma idea: el estado **previo** está primado, el **nuevo** sin primar.

## 3. Ejemplo: cerrar una puerta

El robot ejecuta la acción $u = $ "cerrar puerta" pulsando con su brazo:

![[Acciones - cerrar puerta.png]]
*Robot ejecutando la acción de cerrar la puerta, slide 26.*

El modelo de transición $P(x \mid u, x')$ es una **máquina de estados finitos** con probabilidades en las aristas:

| $x'$ (antes) | $x$ (después) | $P(x \mid u, x')$ |
|---|---|---|
| abierta | cerrada | 0.9 |
| abierta | abierta | 0.1 |
| cerrada | cerrada | 1.0 |
| cerrada | abierta | 0   |

Si la puerta está abierta, la acción "cerrar puerta" tiene **éxito el 90% de las veces**.

![[Acciones - transiciones FSM.png]]
*Diagrama de transiciones de estado para la acción "cerrar puerta", slide 27.*

## 4. Integrando acciones al belief

Para obtener el belief $\bar{Bel}(x_t)$ después de la acción, **marginalizamos sobre el estado anterior** $x_{t-1}$:

### Caso continuo

$$\bar{Bel}(x_t) = \int P(x_t \mid u_t, x_{t-1}) \, Bel(x_{t-1}) \, dx_{t-1}$$

### Caso discreto

$$\bar{Bel}(x_t) = \sum_{x_{t-1}} P(x_t \mid u_t, x_{t-1}) \, Bel(x_{t-1})$$

![[Acciones - integrando formula.png]]
*Integración del modelo de transición sobre el estado anterior, slide 28.*

> [!info] Suposición de independencia
> Asumimos que $P(x_{t-1} \mid u_t) = P(x_{t-1})$ — el estado anterior no depende de la acción que estamos a punto de ejecutar. Esto permite eliminar la $u$ del segundo factor de la suma.

## 5. Ejemplo del belief resultante

Continuando con el ejemplo de la puerta, supongamos que el belief antes de la acción es $P(\text{abierta}) = 5/8$, $P(\text{cerrada}) = 3/8$.

$$P(\text{cerrada} \mid u) = P(\text{cerrada} \mid u, \text{abierta})\,P(\text{abierta}) + P(\text{cerrada} \mid u, \text{cerrada})\,P(\text{cerrada})$$

$$= \frac{9}{10} \cdot \frac{5}{8} + 1 \cdot \frac{3}{8} = \frac{15}{16}$$

Análogamente, $P(\text{abierta} \mid u) = 1/16$.

![[Acciones - belief ejemplo.png]]
*Cálculo del belief resultante después de la acción "cerrar puerta", slide 30.*

> [!warning] Después de la acción, todavía hay incertidumbre
> Aunque la acción "cerrar puerta" es muy buena (90% de éxito), el belief final no es 100% cerrada — porque arrastramos la incertidumbre del belief previo. Las acciones nunca eliminan la incerteza, sólo la propagan.

## 6. Anticipo: la estructura del filtro de Bayes

Con esto tenemos los dos ingredientes:

- **Modelo del sensor**: $P(z \mid x)$ — cómo las observaciones dependen del estado.
- **Modelo de acción**: $P(x \mid u, x')$ — cómo el estado evoluciona con las acciones.
- **Probabilidad a priori** del estado: $P(x)$.

El [[Filtro de Bayes]] es el algoritmo que combina los tres recursivamente para estimar $Bel(x_t) = P(x_t \mid u_1, z_1, \dots, u_t, z_t)$.

## Conexiones
- [[Filtro de Bayes]] — el modelo de movimiento es uno de sus dos ingredientes (paso de predicción).
- [[Modelo de Movimiento (Velocidad)]] — instancia concreta basada en velocidades $(v, \omega)$ — M2.
- [[Odometría y Modelo de Movimiento (Odometría)]] — instancia concreta basada en encoders — M2.
- [[Modelo de Sensor]] — la otra mitad del filtro (paso de actualización).
- [[5. Filtros Bayesianos/Filtros Bayesianos|Módulo 5]] — implementaciones concretas (discretos, Kalman, partículas).

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - slide 23 → 1. Acciones y dinamismo
  - slide 25 → 2. El modelo $P(x \mid u, x')$
  - slides 26–27 → 3. Ejemplo: cerrar una puerta
  - slide 28 → 4. Integrando acciones al belief
  - slides 29–30 → 5. Ejemplo del belief resultante
  - slide 31 → 6. Anticipo: la estructura del filtro de Bayes
