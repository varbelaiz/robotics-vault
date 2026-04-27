---
modulo: 2. Locomoción
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/03-locomocion-3.pdf
  - Raw/Diapositivas/Teoricas/06-modelos-de-movimiento_con_modelo_velocidad-3.pdf
ultima_actualizacion: 2026-04-27
---

> [[Locomoción|← Locomoción]] | [[Robotica|← Inicio]]

# Odometría y Modelo de Movimiento (Odometría)

> Estimar la pose del robot integrando las velocidades de las ruedas (dead reckoning), y modelar la incertidumbre asociada para alimentar un filtro de Bayes.

## Prerequisitos
- [[Cinemática del Robot Diferencial]] — odometría es la integración temporal de la cinemática.
- [[Encoders Ópticos]] — sensor que provee las velocidades de las ruedas.
- [[Regla de Bayes]] — base del modelo probabilístico (forward-ref, M4).

## 1. Odometría y dead reckoning

La **odometría** es la estimación del movimiento del robot basada en las velocidades de sus ruedas, integradas en el tiempo. Es un caso particular de **dead reckoning** ("deduced reckoning" o "dead in the water"): cálculo de la posición integrando velocidades, sin referencias externas.

![[Odometria - dead reckoning trayectoria.png]]
*Odometría sin corrección (izquierda: trayectoria real; derecha: deriva acumulada), slide 34.*

> [!warning] El error se acumula
> Como cada paso integra ruido, la odometría **deriva** sin cota. Por sí sola no sirve para localización a largo plazo — debe combinarse con observaciones del entorno (próximos módulos).

## 2. Por qué necesitamos un modelo probabilístico

El movimiento de un robot es **inherentemente incierto**: ruedas con distintos diámetros, baches, alfombras, patinaje. Para implementar un filtro de Bayes necesitamos un **modelo de transición**:

$$p(x_t \mid x_{t-1}, u_t)$$

Esta probabilidad especifica que la acción $u_t$ lleve al robot desde $x_{t-1}$ hasta $x_t$.

![[Odometria - DBN.png]]
*Red Bayesiana dinámica: estados $x$, controles $u$, observaciones $z$, slide 3.*

## 3. Fuentes de error de robots con ruedas

- Distintos diámetros de ruedas.
- Baches en el terreno.
- Alfombras, deslizamientos.
- Y muchos más.

![[Odometria - fuentes de error ruedas.png]]
*Casos de error: ideal, distintos diámetros, bache, alfombra, slide 9.*

## 4. Modelo de odometría: parametrización

El robot se mueve de $\langle\bar{x}, \bar{y}, \bar{\theta}\rangle$ a $\langle\bar{x}', \bar{y}', \bar{\theta}'\rangle$. La información de odometría se descompone en tres componentes:

$$u = \langle\delta_{rot1}, \delta_{rot2}, \delta_{trans}\rangle$$

con:
$$\delta_{trans} = \sqrt{(\bar{x}' - \bar{x})^2 + (\bar{y}' - \bar{y})^2}$$
$$\delta_{rot1} = \text{atan2}(\bar{y}' - \bar{y},\, \bar{x}' - \bar{x}) - \bar{\theta}$$
$$\delta_{rot2} = \bar{\theta}' - \bar{\theta} - \delta_{rot1}$$

Es decir: **rotar para alinear con la trayectoria → trasladar → rotar a la orientación final**.

![[Odometria - modelo deltas.png]]
*Descomposición de la odometría en $\delta_{rot1}$, $\delta_{trans}$, $\delta_{rot2}$, slide 10.*

> [!info] La función atan2
> $\text{atan2}(y, x)$ extiende la arcotangente para tener en cuenta los signos de $x$ e $y$, devolviendo el ángulo en el cuadrante correcto.

![[Odometria - atan2.png]]
*Definición de atan2 y su superficie continua vs arctan, slide 11.*

## 5. Modelo de ruido para la odometría

El movimiento medido es el verdadero más un ruido $\varepsilon$ con varianza dependiente de las propias deltas:

$$\hat{\delta}_{rot1} = \delta_{rot1} + \varepsilon_{\alpha_1|\delta_{rot1}| + \alpha_2|\delta_{trans}|}$$
$$\hat{\delta}_{trans} = \delta_{trans} + \varepsilon_{\alpha_3|\delta_{trans}| + \alpha_4(|\delta_{rot1}| + |\delta_{rot2}|)}$$
$$\hat{\delta}_{rot2} = \delta_{rot2} + \varepsilon_{\alpha_1|\delta_{rot2}| + \alpha_2|\delta_{trans}|}$$

Los parámetros $\alpha_1, \dots, \alpha_4$ caracterizan el ruido del robot y deben aprenderse empíricamente.

![[Odometria - modelo de ruido.png]]
*Modelo de ruido: cada delta medida es la verdadera más ruido cuya varianza depende de las deltas, slide 12.*

## 6. Distribuciones típicas y operación `prob`

El ruido $\varepsilon_{\sigma^2}$ se modela típicamente con una **normal** o **triangular** centradas en cero. La función `prob(a, b)` que aparece en el algoritmo de la sección 7 es justamente la **evaluación puntual** de esa densidad — ver [[Muestreo de Distribuciones]] para los algoritmos `prob_normal_distribution` y `prob_triangular_distribution`.

## 7. Algoritmo `motion_model_odometry`

Cálculo de la probabilidad $p(x' \mid x, u)$ a posteriori dadas las poses $x, x'$ (la hipótesis) y la odometría $\bar{x}, \bar{x}'$:

```
motion_model_odometry(x, x', x̄, x̄'):
  // parámetros de odometría medidos (u)
  δ_trans = sqrt((x̄' - x̄)² + (ȳ' - ȳ)²)
  δ_rot1  = atan2(ȳ' - ȳ, x̄' - x̄) - θ̄
  δ_rot2  = θ̄' - θ̄ - δ_rot1

  // valores de interés (x, x')
  δ̂_trans = sqrt((x' - x)² + (y' - y)²)
  δ̂_rot1  = atan2(y' - y, x' - x) - θ
  δ̂_rot2  = θ' - θ - δ̂_rot1

  // probabilidades de cada componente
  p1 = prob(δ_rot1 - δ̂_rot1, α₁|δ_rot1| + α₂ δ_trans)
  p2 = prob(δ_trans - δ̂_trans, α₃ δ_trans + α₄(|δ_rot1| + |δ_rot2|))
  p3 = prob(δ_rot2 - δ̂_rot2, α₁|δ_rot2| + α₂ δ_trans)

  return p1 · p2 · p3
```

![[Odometria - motion model algoritmo.png]]
*Algoritmo `motion_model_odometry`, slide 15.*

## 8. La distribución resultante: medialuna

Aplicando el modelo repetidamente para pequeños desplazamientos, la proyección 2D de la distribución a posteriori 3D toma típicamente forma de **"medialuna"**:

![[Odometria - distribucion medialuna.png]]
*La distribución $p(x' \mid u, x)$ tiene forma de medialuna por el acoplamiento entre rotación y traslación, slide 16.*

## 9. Operación `sample`

Para representar la distribución con muestras (necesario para [[MCL - Filtro de Partículas]], M5) la `sample(b)` que aparece en el algoritmo de la próxima sección genera valores que siguen $\varepsilon_{b^2}$. Los algoritmos concretos (suma de 12 uniformes para normal, suma de 2 para triangular, rejection sampling para casos generales) viven en [[Muestreo de Distribuciones]].

## 10. Algoritmo `sample_motion_model` (odometría)

Para muestrear una nueva pose $x'$ dado $u, x$:

```
sample_motion_model(u, x):
  δ̂_rot1  = δ_rot1  + sample(α₁|δ_rot1| + α₂ δ_trans)
  δ̂_trans = δ_trans + sample(α₃ δ_trans + α₄(|δ_rot1| + |δ_rot2|))
  δ̂_rot2  = δ_rot2  + sample(α₁|δ_rot2| + α₂ δ_trans)

  x' = x + δ̂_trans cos(θ + δ̂_rot1)
  y' = y + δ̂_trans sin(θ + δ̂_rot1)
  θ' = θ + δ̂_rot1 + δ̂_rot2

  return ⟨x', y', θ'⟩
```

![[Odometria - sample motion model algoritmo.png]]
*Algoritmo `sample_motion_model` para odometría, slide 28.*

## 11. Ejemplos del modelo

La forma de la distribución cambia según el balance entre los $\alpha_i$ (más ruido en rotación, en traslación, etc.):

![[Odometria - ejemplos sampling.png]]
*Distribuciones $p(x' \mid u, x)$ vs muestras correspondientes para distintos ruidos, slide 29.*

Aplicando repetidamente el modelo de movimiento a una trayectoria conocida, la nube de muestras crece sin cota:

![[Odometria - muestreado trayectoria.png]]
*Muestreo del modelo de movimiento a lo largo de una trayectoria de ~10 m, slide 30.*

## 12. Modelo consistente con mapas

Si conocemos un mapa $m$ del entorno, el modelo $p(x' \mid u, x)$ ignora obstáculos. Una aproximación que **descarta** poses imposibles (atravesar una pared) es:

$$p(x' \mid u, x, m) = \eta \, p(x' \mid m) \, p(x' \mid u, x)$$

donde $p(x' \mid m)$ es 0 si la pose es ocupada y constante si es libre.

![[Movimiento - consistente con mapas.png]]
*El modelo sin mapa permite atravesar paredes; el modelo con mapa descarta esas muestras, slide 31.*

## 13. Variantes y conexiones
- [[Modelo de Movimiento (Velocidad)]] — alternativa cuando no hay encoders, basada en $u = (v, \omega)$.
- [[Muestreo de Distribuciones]] — algoritmos `prob` y `sample` que `motion_model_odometry` y `sample_motion_model` invocan.
- [[MCL - Filtro de Partículas]] — el principal consumidor de `sample_motion_model` (forward-ref, M5).
- [[Filtro de Kalman]] — usa el modelo gaussiano linealizado (forward-ref, M5).
- [[Filtros Discretos]] — consumen `motion_model_odometry` para la actualización de predicción (forward-ref, M5).

## Fuentes
- `Raw/Diapositivas/Teoricas/03-locomocion-3.pdf`
  - slides 34–35 → 1. Odometría y dead reckoning
- `Raw/Diapositivas/Teoricas/06-modelos-de-movimiento_con_modelo_velocidad-3.pdf`
  - slides 2–4 → 2. Por qué necesitamos un modelo probabilístico
  - slide 9 → 3. Fuentes de error de robots con ruedas
  - slide 10 → 4. Modelo de odometría: parametrización
  - slide 12 → 5. Modelo de ruido para la odometría
  - slide 15 → 7. Algoritmo `motion_model_odometry` (delega `prob` a [[Muestreo de Distribuciones]])
  - slide 16 → 8. La distribución resultante: medialuna
  - slide 28 → 10. Algoritmo `sample_motion_model` (delega `sample` a [[Muestreo de Distribuciones]])
  - slides 29–30 → 11. Ejemplos del modelo
  - slide 31 → 12. Modelo consistente con mapas