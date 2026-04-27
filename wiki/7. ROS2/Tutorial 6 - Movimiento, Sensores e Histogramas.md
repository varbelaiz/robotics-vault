---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 6_ Movimiento, Sensores e Histogramas.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# Tutorial 6 — Movimiento, Sensores e Histogramas

> Aterriza los dos ingredientes del [[Filtro de Bayes]]: el **modelo de movimiento** $P(x_t \mid u_t, x_{t-1})$ — con muestreo de odometría ruidosa — y el **modelo de sensor** $P(z_t \mid x_t, m)$ — con beam-based + Bresenham para ray-casting, y likelihood field como alternativa eficiente. Cierra con la discretización del espacio que da pie al [[Filtros Discretos|filtro de histograma]] del TP2.

## Objetivo

Que cada estudiante pueda:

1. Implementar `sample_motion_model_odometry`: dado $(x_{t-1}, u_t)$ y parámetros de ruido, generar muestras de $x_t$.
2. Implementar el modelo de sensor basado en haz, incluyendo el ray-casting con **Bresenham**.
3. Entender por qué el **likelihood field** es preferido como modelo eficiente.
4. Discretizar el espacio para usar un filtro de histograma.

## Conceptos que se aplican
- [[Filtro de Bayes]] — la fórmula que ata los dos modelos.
- [[Odometría y Modelo de Movimiento (Odometría)]] — modelo $P(x_t \mid u_t, x_{t-1})$ basado en encoders.
- [[Modelo de Sensor Basado en Haz]] — mezcla hit/unexp/max/rand.
- [[Modelo de Campo de Verosimilitud]] — alternativa precomputada.
- [[Filtros Discretos]] — implementación con histograma.

## 1. Modelo de movimiento $P(x_t \mid u_t, x_{t-1})$

El modelo de odometría representa el control $u_t$ como una **terna** $(\delta_{rot1}, \delta_{trans}, \delta_{rot2})$:

![[Tutorial 6 - odometria deltas.png]]
*Triángulo de odometría: del estado $\langle\bar{x}, \bar{y}, \bar{\theta}\rangle$ al $\langle\bar{x}', \bar{y}', \bar{\theta}'\rangle$ via primero rotar $\delta_{rot1}$, luego trasladar $\delta_{trans}$, luego rotar $\delta_{rot2}$, slide 3.*

![[Tutorial 6 - odometria formulas deltas.png]]
*Fórmulas: $\delta_{trans} = \sqrt{(\bar{x}' - \bar{x})^2 + (\bar{y}' - \bar{y})^2}$, $\delta_{rot1} = \text{atan2}(\bar{y}' - \bar{y}, \bar{x}' - \bar{x}) - \bar{\theta}$, $\delta_{rot2} = \bar{\theta}' - \bar{\theta} - \delta_{rot1}$, slide 4.*

Detalle teórico completo en [[Odometría y Modelo de Movimiento (Odometría)]] (M2).

### El ruido en la odometría

![[Tutorial 6 - odometria ruido distribuciones.png]]
*Seis distribuciones de $x_t$ después de un mismo movimiento, según el balance de los parámetros de ruido $\alpha_1$–$\alpha_4$. Arriba: PDFs continuas; abajo: muestras del mismo, slide 5.*

> [!info] Cuatro parámetros de ruido
> $\alpha_1, \alpha_2$ ruido en las rotaciones; $\alpha_3, \alpha_4$ ruido en la traslación. Cada $\delta$ se distorsiona por una muestra gaussiana cuya varianza depende de la magnitud del propio movimiento — más se mueve, más incertidumbre.

### `sample_motion_model_odometry`

![[Tutorial 6 - sample motion model odometry.png]]
*Algoritmo: descomponer $u_t$ en deltas, agregar ruido a cada delta (vía `sample(varianza)`), y aplicar la cinemática inversa para obtener $x' = (x', y', \theta')^T$, slide 6.*

```
Algorithm sample_motion_model_odometry(u_t, x_{t-1}):
    δ_rot1  = atan2(ȳ' - ȳ, x̄' - x̄) - θ̄
    δ_trans = √((x̄ - x̄')² + (ȳ - ȳ')²)
    δ_rot2  = θ̄' - θ̄ - δ_rot1

    δ̂_rot1  = δ_rot1  - sample(α₁ δ²_rot1  + α₂ δ²_trans)
    δ̂_trans = δ_trans - sample(α₃ δ²_trans + α₄ δ²_rot1 + α₄ δ²_rot2)
    δ̂_rot2  = δ_rot2  - sample(α₁ δ²_rot2  + α₂ δ²_trans)

    x' = x + δ̂_trans · cos(θ + δ̂_rot1)
    y' = y + δ̂_trans · sin(θ + δ̂_rot1)
    θ' = θ + δ̂_rot1 + δ̂_rot2

    return x_t = (x', y', θ')^T
```

> [!warning] Las varianzas son cuadráticas en los deltas
> `α_i δ²` — no lineal. Un movimiento del doble genera **el cuádruple** de varianza, no el doble.

`sample(b²)` puede implementarse como suma de 12 uniformes (Box-Muller), `numpy.random.normal(0, b)`, o muestreo por rechazo — cualquier método que devuelva una muestra gaussiana de varianza $b^2$ sirve.

## 2. Modelo de sensor $P(z_t \mid x_t, m)$

### El modelo basado en haz

![[Tutorial 6 - mezcla P z x m.png]]
*Mezcla de cuatro componentes con pesos $\alpha_{hit}, \alpha_{unexp}, \alpha_{max}, \alpha_{rand}$ que suman 1. Cada componente tiene su propia forma: hit (gaussiana), unexp (exponencial), max (delta), rand (uniforme), slide 24.*

$$P(z \mid x, m) = \begin{pmatrix}\alpha_{hit}\\ \alpha_{unexp}\\ \alpha_{max}\\ \alpha_{rand}\end{pmatrix}^T \cdot \begin{pmatrix}P_{hit}(z \mid x, m)\\ P_{unexp}(z \mid x, m)\\ P_{max}(z \mid x, m)\\ P_{rand}(z \mid x, m)\end{pmatrix}$$

Para evaluarlo, primero hay que calcular el **rango esperado** $z^{k*}$ por **ray-casting** sobre el mapa $m$ (siguiente sección).

![[Tutorial 6 - beam range finder algoritmo.png]]
*`beam_range_finder_model(z_t, x_t, m)`: para cada haz, ray-casting + evaluar la mezcla de 4 componentes; multiplicar todas las verosimilitudes (asumiendo independencia entre haces), slide 26.*

> [!warning] Dos problemas del beam-based
> 1. **Caro de evaluar**: ray-casting por cada haz, por cada partícula, por cada paso del filtro.
> 2. **No suave**: pequeños cambios en $x_t$ pueden cambiar drásticamente el haz "esperado" si pasa por una pared o no — mata los gradientes que necesitan EKF/UKF.

### Bresenham — ray-casting en grilla

Para encontrar el "punto de contacto" del haz, hay que recorrer la grilla del mapa siguiendo la dirección del rayo. El **algoritmo de Bresenham** es la receta clásica para hacerlo eficientemente con sólo enteros:

![[Tutorial 6 - bresenham linea inicial.png]]
*La línea ideal del haz desde la pose del robot (rojo) hasta el límite de alcance, slide 11.*

![[Tutorial 6 - bresenham celdas avance.png]]
*Bresenham elige, en cada paso, la celda más cercana a la línea ideal. Avanza una celda en x o en y según el error acumulado, slide 20.*

![[Tutorial 6 - bresenham comparacion.png]]
*Comparación: la línea ideal (azul) vs. la secuencia de celdas marcadas (verde sobre gris) — Bresenham aproxima la línea recta con celdas enteras, slide 22.*

> [!info] Por qué Bresenham y no aritmética flotante
> Implementación con sólo sumas y comparaciones de enteros — **muy** rápido. Esto importa porque el ray-casting se hace dentro del loop de cada haz × partícula × tick del filtro.

### Likelihood Field — la alternativa eficiente

![[Tutorial 6 - occupancy vs likelihood field.png]]
*Izq: occupancy grid (negro = ocupado, blanco = libre). Der: likelihood field (escala de grises = distancia al obstáculo más cercano), slide 27.*

La idea: en vez de hacer ray-casting cada vez, **precomputar** una vez un campo escalar donde cada celda guarda la **distancia al obstáculo más cercano**. Luego, por cada haz $z_t^k$:

1. Calcular el endpoint $(x_{z_t^k}, y_{z_t^k})$ del haz en el frame del mapa (proyectar la lectura del lidar usando la pose del robot y la posición del sensor).
2. Lookup la distancia $d$ en el likelihood field.
3. Evaluar una **gaussiana** en $d$ — si el endpoint cae cerca de un obstáculo, $d \approx 0$ → likelihood alta.

![[Tutorial 6 - likelihood field render.png]]
*El likelihood field tras precómputo: las celdas cerca de obstáculos tienen alto valor (amarillo), lejos cae a cero (negro), slide 32.*

![[Tutorial 6 - likelihood field algoritmo.png]]
*`likelihood_field_range_finder_model(z_t, x_t, m)`: proyecta cada haz, busca la distancia al obstáculo más cercano, y evalúa una gaussiana sobre esa distancia, slide 33.*

> [!info] Doble ventaja del likelihood field
> 1. **Eficiente**: lookup en una tabla precomputada, no ray-casting.
> 2. **Suave**: la gaussiana es continua y diferenciable → bueno para EKF/MCL.

### Aplicación: scan completo a likelihood

![[Tutorial 6 - scan a likelihood field.png]]
*De izq a der: el polar plot del LIDAR (un scan), el likelihood field del mapa, y el producto de likelihoods evaluado para una pose hipotética del robot, slide 35.*

El producto de likelihoods de todos los haces es la **likelihood total** de esa pose hipotética dada el scan. Esto se usa directo en MCL ([[MCL - Filtro de Partículas]]) para pesar partículas.

Detalle teórico completo en [[Modelo de Campo de Verosimilitud]] (M3).

## 3. Discretización del espacio

Cuando el espacio es finito (o se discretiza), el filtro de Bayes se simplifica a sumas en vez de integrales:

![[Tutorial 6 - discretizacion algoritmo.png]]
*Izq: estado discretizado en celdas $L_t = l$ con belief sobre cada celda. Der: algoritmo `Discrete_Bayes_filter`: para cada celda $k$, $\bar{p}_{k,t} = \sum_i p(x_k \mid u_t, x_i)\, p_{i,t-1}$ (predicción), luego $p_{k,t} = \eta\, p(z_t \mid x_k)\, \bar{p}_{k,t}$ (actualización), slide 36.*

```
Algorithm Discrete_Bayes_filter({p_{k,t-1}}, u_t, z_t):
    for all k do
        p̄_{k,t} = Σ_i  p(X_t = x_k | u_t, X_{t-1} = x_i) · p_{i,t-1}      # predicción
        p_{k,t} = η · p(z_t | X_t = x_k) · p̄_{k,t}                        # actualización
    return {p_{k,t}}
```

### Modelo de movimiento discreto (preview TP2)

![[Tutorial 6 - filtro discreto modelo movimiento.png]]
*Modelo de movimiento simple del TP2: al ejecutar "avanzar un paso" hay 25% no se mueve, 50% una celda, 25% dos celdas. Esto define $P(x_t \mid u_t, x_{t-1})$ como una distribución sobre 3 valores, slide 37.*

> [!info] Convolución
> El modelo discreto se aplica como una **convolución** del belief con un kernel $[0.25, 0.5, 0.25]$ desplazado. El paso de predicción es $O(n)$ en lugar de $O(n^2)$.

Detalle completo en [[Filtros Discretos]] (M5).

## Conexiones
- [[Tutorial 5 - Bayes]] — tutorial anterior (Bayes recursivo).
- [[Tutorial 7 - Filtro de Partículas]] — siguiente: MCL implementa el modelo de movimiento + sensor sobre un set de partículas.
- [[Filtro de Bayes]] — la fórmula que cierra los dos modelos.
- [[Odometría y Modelo de Movimiento (Odometría)]] / [[Modelo de Sensor Basado en Haz]] / [[Modelo de Campo de Verosimilitud]] / [[Filtros Discretos]] — teoría que respalda cada sección.
- [[TP2 - Modelos Probabilísticos y Filtros Discretos]] — primer TP que implementa todo esto.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 6_ Movimiento, Sensores e Histogramas.pdf`
  - slides 2–7 → 1. Modelo de movimiento (deltas, ruido, sample_motion_model_odometry)
  - slides 8–9 → 2. Modelo de sensor (motivación)
  - slides 10–22 → 2.1 Bresenham (ray-casting en grilla)
  - slides 23–26 → 2.2 Beam-based (mezcla, problemas, algoritmo)
  - slides 27–35 → 2.3 Likelihood Field (motivación, render, algoritmo)
  - slides 36–37 → 3. Discretización del espacio + preview TP2
