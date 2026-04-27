---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf
ultima_actualizacion: 2026-04-27
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Derivación del Filtro de Bayes

> Cómo se llega a las ecuaciones de predicción y actualización partiendo de la definición del belief y aplicando Bayes + Markov + probabilidad total.

## Punto de partida: el belief

Queremos calcular la probabilidad a posteriori del estado en el tiempo $t$, dados todos los comandos y mediciones hasta ese momento:

$$Bel(x_t) = P(x_t \mid z_{1:t},\, u_{1:t})$$

La historia completa de mediciones $z_1, \ldots, z_t$ y acciones $u_1, \ldots, u_t$.

---

## Paso 1 — Separar la última medición (Regla de Bayes)

Separamos $z_t$ del resto de la historia y aplicamos Bayes:

$$P(x_t \mid z_{1:t}, u_{1:t}) = \frac{P(z_t \mid x_t,\, z_{1:t-1},\, u_{1:t}) \cdot P(x_t \mid z_{1:t-1},\, u_{1:t})}{P(z_t \mid z_{1:t-1},\, u_{1:t})}$$

El denominador no depende de $x_t$ — es una constante de normalización. Lo llamamos $\eta$:

$$= \eta \cdot P(z_t \mid x_t,\, z_{1:t-1},\, u_{1:t}) \cdot P(x_t \mid z_{1:t-1},\, u_{1:t})$$

---

## Paso 2 — Suposición de Markov sobre la medición

El término $P(z_t \mid x_t,\, z_{1:t-1},\, u_{1:t})$ dice: "la medición actual depende de todo el historial". Pero si ya sabés el estado actual $x_t$, la historia pasada no agrega información sobre $z_t$. Esto es la **[[Suposición de Markov]]**:

$$P(z_t \mid x_t,\, z_{1:t-1},\, u_{1:t}) = P(z_t \mid x_t)$$

Entonces:

$$Bel(x_t) = \eta \cdot P(z_t \mid x_t) \cdot P(x_t \mid z_{1:t-1},\, u_{1:t})$$

El segundo factor, $P(x_t \mid z_{1:t-1},\, u_{1:t})$, es el belief *después de la acción $u_t$ pero antes de ver $z_t$. Lo llamamos $\bar{Bel}(x_t)$.

Con esto ya tenemos la **ecuación de actualización**:

$$\boxed{Bel(x_t) = \eta \cdot P(z_t \mid x_t) \cdot \bar{Bel}(x_t)}$$

---

## Paso 3 — Derivar $\bar{Bel}(x_t)$: la predicción

Ahora expandimos $\bar{Bel}(x_t) = P(x_t \mid z_{1:t-1},\, u_{1:t})$.

Aplicamos la **ley de probabilidad total**, marginalizando sobre todos los estados anteriores posibles $x_{t-1}$:

$$P(x_t \mid z_{1:t-1},\, u_{1:t}) = \int P(x_t \mid x_{t-1},\, z_{1:t-1},\, u_{1:t}) \cdot P(x_{t-1} \mid z_{1:t-1},\, u_{1:t}) \, dx_{t-1}$$

**Suposición de Markov sobre la transición**: dado $x_{t-1}$ y la acción $u_t$, el nuevo estado $x_t$ no depende del historial pasado:

$$P(x_t \mid x_{t-1},\, z_{1:t-1},\, u_{1:t}) = P(x_t \mid x_{t-1},\, u_t)$$

**Independencia del estado anterior respecto a la acción futura**: el estado $x_{t-1}$ ya ocurrió; la acción $u_t$ que se ejecuta ahora no lo afecta retroactivamente:

$$P(x_{t-1} \mid z_{1:t-1},\, u_{1:t}) = P(x_{t-1} \mid z_{1:t-1},\, u_{1:t-1}) = Bel(x_{t-1})$$

Juntando todo:

$$\boxed{\bar{Bel}(x_t) = \int P(x_t \mid u_t,\, x_{t-1}) \cdot Bel(x_{t-1}) \, dx_{t-1}}$$

Eso es la **ecuación de predicción**.

---

## Resultado final: el ciclo completo

Partiendo de $P(x_t \mid z_{1:t}, u_{1:t})$ y aplicando Bayes + Markov + probabilidad total llegamos a:

$$\bar{Bel}(x_t) = \int P(x_t \mid u_t,\, x_{t-1}) \cdot Bel(x_{t-1}) \, dx_{t-1} \qquad \text{(predicción)}$$

$$Bel(x_t) = \eta \cdot P(z_t \mid x_t) \cdot \bar{Bel}(x_t) \qquad \text{(actualización)}$$

| Paso | Operación | Efecto sobre el belief |
|---|---|---|
| Predicción | Integrar el modelo de movimiento | **Aumenta** la incertidumbre |
| Actualización | Ponderar por la verosimilitud del sensor | **Reduce** la incertidumbre |

## Resumen de supuestos usados

1. **Regla de Bayes** — para separar $z_t$ del resto e invertir la condicional.
2. **Suposición de Markov** (×2) — para que $z_t$ dependa sólo de $x_t$, y $x_t$ dependa sólo de $x_{t-1}$ y $u_t$.
3. **Ley de probabilidad total** — para marginalizar sobre $x_{t-1}$ en la predicción.
4. **Independencia acción/estado anterior** — $u_t$ no afecta retroactivamente a $x_{t-1}$.

> [!info] ¿Por qué es factible?
> Sin la Suposición de Markov, la integral de predicción dependería de toda la historia $z_{1:t-1}, u_{1:t-1}$, que crece sin límite. Markov "corta" esa cadena: el presente resume todo el pasado relevante.

## Conexiones
- [[Regla de Bayes]] — herramienta del Paso 1
- [[Suposición de Markov]] — herramienta del Paso 2 y Paso 3
- [[Filtro de Bayes]] — el algoritmo resultante
- [[Modelo de Movimiento]] — $P(x_t \mid u_t, x_{t-1})$, entra en la predicción
- [[Modelo de Sensor]] — $P(z_t \mid x_t)$, entra en la actualización

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf`
  - slides 33–40 → derivación completa paso a paso
