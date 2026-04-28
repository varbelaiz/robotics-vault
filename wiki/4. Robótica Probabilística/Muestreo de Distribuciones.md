---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/06-modelos-de-movimiento_con_modelo_velocidad-3.pdf
ultima_actualizacion: 2026-04-28
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Muestreo de Distribuciones

> Hub de las dos operaciones básicas sobre distribuciones de probabilidad: **evaluar** la densidad en un punto (`prob`) y **muestrear** valores que la sigan (`sample`). Son los bloques que arman tanto los modelos de movimiento como el filtro de partículas.

## Prerequisitos
- [[Variables Aleatorias Continuas]] — densidad de probabilidad.
- [[Axiomas de Probabilidad]] — propiedades básicas.

## 1. Por qué importan estas dos operaciones

Los modelos de movimiento de [[Modelo de Movimiento (Odometría)]] y [[Modelo de Movimiento (Velocidad)]] usan ruido $\varepsilon$ con varianza $\sigma^2$. El ruido aparece de dos maneras:

- **Como densidad** en `motion_model_*`: dadas dos poses, calcular $p(x_t \mid u_t, x_{t-1})$ multiplicando densidades puntuales — necesita `prob(a, b)`.
- **Como muestras** en `sample_motion_model_*`: generar una nueva pose $x_t$ a partir de $u_t, x_{t-1}$ — necesita `sample(b)`.

La misma dualidad reaparece en [[MCL - Filtro de Partículas]] (predict ↔ sample, update ↔ prob).

## 2. Distribuciones canónicas

El curso usa dos distribuciones centradas en cero como modelo de ruido:

|                                | Normal                                                                  | Triangular                                                                              |
| ------------------------------ | ----------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| $\varepsilon_{\sigma^2}(x) = $ | $\dfrac{1}{\sqrt{2\pi\sigma^2}} e^{-\tfrac{1}{2}\tfrac{x^2}{\sigma^2}}$ | $\dfrac{\sqrt{6\sigma^2} - \|x\|}{6\sigma^2}$ si $\|x\| \le \sqrt{6\sigma^2}$, sino $0$ |

![[Odometria - distribuciones normal y triangular.png]]
*Las dos densidades canónicas, slide 13.*

> [!info] Por qué triangular
> Es más rápida de muestrear y de evaluar que la normal, y empíricamente da resultados similares en filtros — un trade-off común en robótica embebida.

## 3. Evaluar densidad: `prob(a, b)`

Dado un punto $a$ y un desvío estándar $b$, devolver la densidad puntual $\varepsilon_{b^2}(a)$:

```
prob_normal_distribution(a, b):
  return (1 / sqrt(2π·b²)) · exp( -½ · a²/b² )

prob_triangular_distribution(a, b):
  return max( 0,  1/(√6·b)  -  |a| / (6·b²) )
```

![[Muestreo - prob algoritmos.png]]
*Algoritmos `prob_normal_distribution` y `prob_triangular_distribution`, slide 14.*

> [!info] Notación
> En las slides $b$ es el **desvío estándar**, no la varianza. Por eso aparece $b^2$ en los denominadores y $\sqrt{6}\,b$ en el rango de la triangular.

## 4. Representación de densidad por muestras

Una alternativa a guardar la **función analítica** de una densidad es guardar **muchas muestras** que la sigan: la densidad queda representada implícitamente por la concentración de muestras.

![[Muestreo - densidad muestras.png]]
*Una densidad arbitraria $f$ y un conjunto de muestras tomadas de ella (rugosa azul), slide 18.*

Es la idea base del [[MCL - Filtro de Partículas]]: en vez de propagar una gaussiana o una grilla, propaga partículas.

## 5. Muestrear normal: suma de uniformes

Por el **teorema central del límite**, sumar muchas uniformes y normalizar produce algo aproximadamente normal:

```
sample_normal_distribution(b):
  1. Generar k muestras U_i ~ U(0,1)   (típicamente k = 12)
  2. Z = Σ U_i  -  k/2                  (centrar en 0)
  3. Z' = Z / sqrt(k/12)                (varianza estándar)
  4. return  μ + b · Z'                  (media μ y desvío b)
```

![[Muestreo - sample normal algoritmo.png]]
*Algoritmo de muestreo de una normal por suma de 12 uniformes, slide 19.*

Convergencia visual con el número de muestras:

![[Muestreo - histograma normal.png]]
*1.000 muestras (izq.) vs 1.000.000 muestras (der.) — la campana emerge con $n$ grande, slide 20.*

> [!info] Por qué k = 12
> Para $k=12$ la varianza de $Z$ es exactamente $1$, así que $Z' = Z$ y se ahorra una división. Es un truco numérico clásico.

## 6. Muestrear triangular: suma de dos uniformes

```
sample_triangular_distribution(b):
  1. Generar U_1, U_2 ~ U(0,1)
  2. S = U_1 + U_2
  3. return  -b + b · S
```

![[Muestreo - sample triangular algoritmo.png]]
*Algoritmo de muestreo de una triangular por suma de 2 uniformes, slide 21.*

![[Muestreo - histograma triangular.png]]
*1.000 muestras (izq.) vs 1.000.000 muestras (der.) — el triángulo emerge claramente, slide 22.*

## 7. Muestrear distribución arbitraria: rejection sampling

Cuando $f$ no es una de las canónicas — y no se conoce su forma analítica simple — se usa **muestreo por rechazo**:

```
sample_distribution(f, b):
  repeat:
    x = rand(-b, b)
    y = rand(0, max{f(x) | x ∈ [-b, b]})
  until  y ≤ f(x)
  return x
```

Geométricamente: tirar puntos uniformes en el rectángulo $[-b, b] \times [0, \max f]$ y conservar sólo los que caen **bajo** la curva $f$.

![[Rejection sampling - visual.png]]
*Idea visual: el punto $x'$ está bajo $f$ → se acepta; el punto $x$ queda por arriba → se descarta, slide 24.*

![[Muestreo - sample distribution algoritmo.png]]
*Pseudocódigo de `sample_distribution`, slide 25.*

> [!warning] Eficiencia
> El rejection sampling es **simple pero lento** cuando $f$ es muy puntiaguda — la mayoría de los puntos se rechazan. Para distribuciones complejas hay alternativas (importance sampling, MCMC) — ver [[Muestreo por Importancia]] (M5).

### Ejemplos

Muestreando $f(x) = |x|$ en $[-1, 1]$ con rejection sampling, el histograma converge a un perfil triangular centrado en los extremos:

![[Muestreo - ejemplo abs.png]]
*Histograma de $10^6$ muestras de $f(x) = |x|$, slide 26.*

Y para $f(x) = \cos(x)$ en $[-1, 1]$:

![[Muestreo - ejemplo cos.png]]
*Histograma de $10^6$ muestras de $f(x) = \cos(x)$, slide 27.*

## 8. Dónde se usa cada operación

| Operación | Algoritmo que la consume | Aplicación |
|---|---|---|
| `prob_normal_distribution` | [[Modelo de Movimiento (Odometría)\|`motion_model_odometry`]], [[Modelo de Movimiento (Velocidad)\|`motion_model_velocity`]] | Evaluar $p(x_t \mid u_t, x_{t-1})$ |
| `sample_normal_distribution` | [[Modelo de Movimiento (Odometría)\|`sample_motion_model_odometry`]], [[Modelo de Movimiento (Velocidad)\|`sample_motion_model_velocity`]] | Generar partículas en [[MCL - Filtro de Partículas\|MCL]] |
| `sample_distribution` (rechazo) | Cualquier $f$ no estándar | Investigación / debugging de modelos custom |

## Conexiones
- [[Modelo de Movimiento (Odometría)]] — primer consumidor: `prob` en el modelo de medida y `sample` para partículas.
- [[Modelo de Movimiento (Velocidad)]] — mismo rol con $u = (v, \omega)$.
- [[MCL - Filtro de Partículas]] — usa `sample_motion_model` partícula a partícula.
- [[TP2 - Modelos Probabilísticos y Filtros Discretos]] — implementa los tres muestreadores de gaussiana (suma de 12, rechazo, Box-Muller) como ejercicio.
- [[Muestreo por Importancia]] (M5) — alternativa más eficiente al rejection sampling.

## Fuentes
- `Raw/Diapositivas/Teoricas/06-modelos-de-movimiento_con_modelo_velocidad-3.pdf`
  - slide 13 → 2. Distribuciones canónicas
  - slide 14 → 3. Evaluar densidad: `prob(a, b)`
  - slides 17–18 → 4. Representación de densidad por muestras
  - slides 19–20 → 5. Muestrear normal: suma de uniformes
  - slides 21–22 → 6. Muestrear triangular: suma de dos uniformes
  - slides 23–25 → 7. Muestrear distribución arbitraria: rejection sampling
  - slides 26–27 → 7. Ejemplos
