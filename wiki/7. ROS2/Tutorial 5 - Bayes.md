---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 5_ Bayes.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# Tutorial 5 — Bayes

> Aplicación práctica de la [[Regla de Bayes]] a un ejercicio canónico (sensor de rango defectuoso), con desarrollo paso a paso, generalización recursiva, código Python y plot. Cierra con una preview de **fusión de sensores** y la derivación del [[Filtro de Bayes]].

## Objetivo

Resolver un problema realista de inferencia con Bayes: dadas $N$ mediciones del sensor todas reportando "$<1$ m", **¿cuál es la probabilidad posterior de que el sensor esté defectuoso?**

## Conceptos que se aplican
- [[Regla de Bayes]] — la fórmula central.
- [[Probabilidad Condicional]] — desarrollo de $P(\text{medicion}_1)$ via casos.
- [[Probabilidad Conjunta]] — descomposición $P(x, y) = P(x|y) P(y)$.
- [[Filtro de Bayes]] — generalización recursiva (slides 29–39).

## 1. Enunciado del problema

![[Tutorial 5 - enunciado sensor.png]]
*Enunciado completo: sensor de rango 0–3 m con valores reales uniformes; cuando está defectuoso reporta siempre $<1$ m; prior de defectuoso $p = 0.01$; pregunta $P(\text{defectuoso} \mid N \text{ mediciones} < 1\text{m})$ para $N=1,\dots,10$, slide 3.*

**Datos**:
- Estado posible: $\{\text{sensor\_defectuoso}, \text{sensor\_correcto}\}$.
- Cuando está defectuoso, **siempre** reporta $<1$ m → $P(z<1 \mid \text{defectuoso}) = 1$.
- Cuando está correcto, los rangos reales son uniformes en $[0, 3]$ m → $P(z<1 \mid \text{correcto}) = 1/3$.
- Prior: $P(\text{defectuoso}) = 0.01$, $P(\text{correcto}) = 0.99$.

## 2. Una sola medición ($N=1$)

Aplicación directa de Bayes:

$$P(\text{def} \mid z_1) = \frac{P(z_1 \mid \text{def})\,P(\text{def})}{P(z_1)}$$

El numerador es trivial: $1 \cdot 0.01 = 0.01$. El denominador (la **evidencia** $P(z_1)$) requiere desarrollo:

![[Tutorial 5 - decomposicion evidencia.png]]
*La evidencia $P(z_1 < 1)$ se decompone marginalizando sobre el estado del sensor: certera + errónea, slide 9.*

Por probabilidad total:

$$P(z_1) = P(z_1, \text{correcto}) + P(z_1, \text{defectuoso})$$

Aplicando $P(x, y) = P(x \mid y) P(y)$ a cada término:

![[Tutorial 5 - medicion certera uniforme.png]]
*$P(z_1, \text{correcto}) = P(z_1 \mid \text{correcto}) \cdot P(\text{correcto}) = \tfrac{1}{3} \cdot 0.99$. La fracción $1/3$ viene de la uniforme en $[0, 3]$, slide 10.*

Y el otro término:

- $P(z_1, \text{defectuoso}) = 1 \cdot 0.01 = 0.01$

Sumando:

$$P(z_1) = \tfrac{1}{3} \cdot 0.99 + 1 \cdot 0.01 = 0.34$$

![[Tutorial 5 - bayes valores numericos.png]]
*Tabla con todos los valores: likelihood = 1, prior = 0.01, evidencia = 1/3·0.99 + 1·0.01, slide 13.*

Resultado:

![[Tutorial 5 - posterior N1 resultado.png]]
*$P(\text{def} \mid z_1) = \frac{1 \cdot 0.01}{1/3 \cdot 0.99 + 1 \cdot 0.01} \approx 0.0294$, slide 14.*

> [!info] Lectura
> Una sola medición $<1$m sólo triplica el prior (de 1% a ~3%). Con un solo dato, el sensor probablemente está bien y la medición es genuina (caímos en el primer metro de los 3 disponibles).

## 3. Dos mediciones ($N=2$)

Generalizamos aplicando Bayes con el conocimiento de fondo $z_1$:

$$P(\text{def} \mid z_2, z_1) = \frac{P(z_2 \mid \text{def}, z_1)\,P(\text{def} \mid z_1)}{P(z_2 \mid z_1)}$$

Por la [[Suposición de Markov]], $P(z_2 \mid \text{def}, z_1) = P(z_2 \mid \text{def}) = 1$ — el sensor defectuoso se comporta independiente de mediciones pasadas.

El numerador es entonces $1 \cdot P(\text{def} \mid z_1) = 1 \cdot 0.0294$.

El denominador $P(z_2 \mid z_1)$ se descompone otra vez por probabilidad total, pero con el **prior actualizado**:

$$P(z_2 \mid z_1) = P(z_2 \mid \text{def}) \cdot P(\text{def} \mid z_1) + P(z_2 \mid \text{correcto}) \cdot P(\text{correcto} \mid z_1)$$

$$= 1 \cdot 0.0294 + \tfrac{1}{3} \cdot (1 - 0.0294)$$

![[Tutorial 5 - posterior N2 resultado.png]]
*$P(\text{def} \mid z_2, z_1) = \frac{1 \cdot 0.0294}{1 \cdot 0.0294 + 1/3 \cdot (1 - 0.0294)} \approx 0.0833$, slide 22.*

Cada nueva medición $<1$m **multiplica el belief actual por la likelihood** y re-normaliza. Es la [[Regla de Bayes#Actualización recursiva combinando evidencia|actualización recursiva]] que vimos en el módulo 4.

## 4. Generalización recursiva en código

![[Tutorial 5 - codigo recursivo python.png]]
*Función `calculate_prob(N)` que aplica Bayes recursivamente. El loop hace `prev = 1 * prev / (1 * prev + 1/3 * (1-prev))`, slide 23.*

```python
import matplotlib.pyplot as plt

def calculate_prob(N):
    prev = 0.01 / (1/3 * 0.99 + 0.01)  # N=1
    if N == 1:
        return prev
    for _ in range(N - 1):
        prev = 1 * prev / (1 * prev + (1/3) * (1 - prev))
    return prev

record = [calculate_prob(N) for N in range(1, 11)]

plt.plot(record)
plt.xlabel('Number of Iterations')
plt.ylabel('Probability of machine broken')
plt.show()
```

### Plot del posterior vs. $N$

![[Tutorial 5 - plot posterior vs N.png]]
*El posterior crece de 0.03 (N=1) a ~1.0 (N=10). Después de 6 mediciones consecutivas $<1$m, prácticamente la única explicación es que el sensor esté defectuoso, slide 24.*

> [!warning] Por qué crece tan rápido
> El cociente de likelihoods $\frac{P(z \mid \text{def})}{P(z \mid \text{correcto})} = \frac{1}{1/3} = 3$. Cada medición consecutiva $<1$m multiplica el odds ratio por 3, así que tras $N$ mediciones el odds es $3^N$ veces el inicial — exponencial.

> [!info] ¿Y si llega 1 medición $\geq 1$m? *(slide 25)*
> Una sola medición fuera del rango "defectuoso" hace $P(z \mid \text{def}) = 0$ → posterior pasa instantáneamente a 0. Una observación contradictoria mata la hipótesis.

## 5. Fusión de sensores

![[Tutorial 5 - fusion sensores tabla.png]]
*Robot afuera/adentro de un edificio con dos sensores: luz (P("hay luz" | adentro) = 0.3, P("hay luz" | afuera) = 0.8) y GPS (P(señal | adentro) = 0.2, P(señal | afuera) = 0.9), slides 27-28.*

Con dos sensores **independientes**, la actualización es secuencial: aplicamos Bayes con la primera medición, y el posterior resultante se usa como prior para la segunda.

| Llega | Posterior $P(\text{adentro} \mid z)$ |
|---|---|
| solo "Hay luz" | $\frac{0.3 \cdot 0.5}{0.3 \cdot 0.5 + 0.8 \cdot 0.5} = 0.273$ |
| solo "Hay señal" | $\frac{0.2 \cdot 0.5}{0.2 \cdot 0.5 + 0.9 \cdot 0.5} = 0.182$ |
| ambos | aplicar las dos en cualquier orden — el resultado es el mismo |

> [!info] Si los sensores son condicionalmente independientes dado el estado
> $P(z_{\text{luz}}, z_{\text{gps}} \mid \text{adentro}) = P(z_{\text{luz}} \mid \text{adentro}) \cdot P(z_{\text{gps}} \mid \text{adentro})$. Esta es la suposición que permite la fusión secuencial sin error.

## 6. Hacia el filtro de Bayes

Las últimas slides (29–39) re-derivan el [[Filtro de Bayes]] paso a paso a partir de Bayes + Markov + probabilidad total + independencia, llegando a:

$$Bel(x_t) = \eta\,P(z_t \mid x_t) \int P(x_t \mid u_t, x_{t-1})\,Bel(x_{t-1})\,dx_{t-1}$$

Esta es **exactamente la fórmula** del Módulo 4 — el tutorial la rederiva como cierre. Detalle completo en [[Filtro de Bayes#Derivación del algoritmo recursivo]].

> [!info] Cómo obtener $P(z_t \mid x_t)$ y $P(x_t \mid u_t, x_{t-1})$ *(slide 39)*
> 1. **Especificadas por la consigna** del TP (caso TP2 — el enunciado da el modelo de movimiento y de sensor).
> 2. **Filtros gaussianos / no paramétricos** — Kalman, MCL, etc., aprenden o asumen formas paramétricas (Módulo 5).

## Conexiones
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — tutorial anterior.
- [[Tutorial 6 - Movimiento, Sensores e Histogramas]] — tutorial siguiente, aplica modelos a histograma.
- [[Regla de Bayes]] — base teórica del ejercicio.
- [[Probabilidad Condicional]] / [[Probabilidad Conjunta]] — herramientas del desarrollo.
- [[Suposición de Markov]] — habilita la actualización recursiva.
- [[Filtro de Bayes]] — derivación que cierra el tutorial.
- [[TP2 - Modelos Probabilísticos y Filtros Discretos]] — primer TP donde la consigna especifica los modelos $P(z|x)$ y $P(x|u,x')$.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 5_ Bayes.pdf`
  - slides 1–3 → 1. Enunciado del problema
  - slides 4–14 → 2. Una sola medición ($N=1$)
  - slides 15–22 → 3. Dos mediciones ($N=2$)
  - slides 23–25 → 4. Generalización recursiva en código
  - slides 26–28 → 5. Fusión de sensores
  - slides 29–39 → 6. Hacia el filtro de Bayes
