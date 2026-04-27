---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Probabilidad Condicional

> La probabilidad de un evento dado que otro evento ha ocurrido.

## Definición

$P(x \mid y)$ es la probabilidad de $x$ dado $y$:

$$P(x \mid y) = \frac{P(x, y)}{P(y)}$$

### Regla de reescritura

$$P(x, y) = P(x \mid y) \cdot P(y)$$

### Independencia

Si $X$ e $Y$ son independientes, entonces:

$$P(x \mid y) = P(x)$$

El conocimiento de $y$ no modifica la probabilidad de $x$.

## Causal vs. Diagnóstico

![[Bayes - causal vs diagnostico.png]]
*Razonamiento causal vs. diagnóstico: el sensor da $P(z \mid x)$, queremos $P(x \mid z)$, slide 17.*

En robótica hay dos direcciones de razonamiento:

- **Causal**: $P(z \mid x)$ — dada la posición, ¿qué medición espero? (fácil de obtener: lo da el modelo de sensor)
- **Diagnóstico**: $P(x \mid z)$ — dada la medición, ¿dónde estoy? (difícil de calcular directamente)

La **Regla de Bayes** permite convertir conocimiento causal en razonamiento diagnóstico.

### Ejemplo: puerta

![[Bayes - puerta abierta.png]]
*El robot observa una puerta y debe decidir si está abierta o cerrada, slide 18.*

Un robot mide si una puerta está abierta o cerrada con un sensor imperfecto:

- $P(z \mid \text{abierta}) = 0.6$
- $P(z \mid \neg\text{abierta}) = 0.3$
- Prior uniforme: $P(\text{abierta}) = P(\neg\text{abierta}) = 0.5$

![[Bayes - ejemplo numerico puerta.png]]
*Cálculo numérico aplicando Bayes al ejemplo de la puerta, slide 19.*

Aplicando Bayes:

$$P(\text{abierta} \mid z) = \frac{P(z \mid \text{abierta})\,P(\text{abierta})}{P(z)} = \frac{0.6 \cdot 0.5}{0.6 \cdot 0.5 + 0.3 \cdot 0.5} = \frac{0.3}{0.45} = \frac{2}{3}$$

La medición $z$ eleva la probabilidad de que la puerta esté abierta de $0.5$ a $\approx 0.67$.

## Conexiones
- [[Regla de Bayes]] — inversión de la condicional
- [[Probabilidad Conjunta]] — base para definir la condicional
- [[Modelo de Sensor]] — $P(z \mid x)$ es una probabilidad condicional

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - pág. 10 → Definición
  - págs. 18–19 → Causal vs. Diagnóstico