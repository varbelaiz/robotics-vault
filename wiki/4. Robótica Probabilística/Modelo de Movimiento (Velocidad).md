---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/06-modelos-de-movimiento_con_modelo_velocidad-3.pdf
ultima_actualizacion: 2026-04-28
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Modelo de Movimiento (Velocidad)

> Modelo probabilístico del movimiento cuando el control viene como velocidad lineal y angular $u = (v, \omega)$ — útil cuando no hay encoders en las ruedas (e.g. con IMU).

## Prerequisitos
- [[Modelo de Movimiento]] — el hub conceptual de $P(x \mid u, x')$.
- [[Cinemática del Robot Diferencial]] — la idea de ICC reaparece acá (M2).
- [[Modelo de Movimiento (Odometría)]] — modelo paralelo basado en encoders.

## 1. Cuándo usar este modelo

Existen dos modelos de movimiento clásicos:

- **Basado en odometría**: para sistemas equipados con **encoders** en las ruedas.
- **Basado en velocidad** (dead reckoning): cuando **no hay encoders**. Calcula la nueva pose basándose en las velocidades comandadas y el tiempo transcurrido (e.g. usando una [[IMU - Acelerómetro, Giróscopo, Magnetómetro|IMU]]).

## 2. Setup: control como velocidad

El control es:
$$u = (v, \omega)^T$$

donde $v$ es la velocidad lineal y $\omega$ la velocidad angular. La trayectoria entre $x_{t-1}$ y $x_t$ es un **arco de círculo** de radio $r = v/\omega$ alrededor del ICC.

![[Velocidad - modelo setup.png]]
*El robot se mueve sobre un arco con radio $r = v/\omega$, ICC en $\langle x_c, y_c\rangle$, slide 34.*

## 3. Modelo de ruido (primera versión)

La velocidad medida es la verdadera más ruido proporcional a las velocidades:

$$\hat{v} = v + \varepsilon_{\alpha_1|v| + \alpha_2|\omega|}$$
$$\hat{\omega} = \omega + \varepsilon_{\alpha_3|v| + \alpha_4|\omega|}$$

![[Velocidad - modelo de ruido.png]]
*Modelo de ruido inicial con dos componentes, slide 35.*

> [!warning] Limitación del modelo de 2 parámetros
> El círculo $(\hat{v}, \hat{\omega})$ limita la orientación final a un manifold 2D dentro del espacio 3D — no se puede explorar toda la pose final.

## 4. Modelo extendido con tercer parámetro

La solución es agregar un tercer ruido $\hat{\gamma}$ que contempla la **rotación final**:

$$\hat{v} = v + \varepsilon_{\alpha_1|v| + \alpha_2|\omega|}$$
$$\hat{\omega} = \omega + \varepsilon_{\alpha_3|v| + \alpha_4|\omega|}$$
$$\hat{\gamma} = \varepsilon_{\alpha_5|v| + \alpha_6|\omega|}$$

Y las ecuaciones del modelo:

$$x' = x - \frac{\hat{v}}{\hat{\omega}}\sin\theta + \frac{\hat{v}}{\hat{\omega}}\sin(\theta + \hat{\omega}\Delta t)$$
$$y' = y + \frac{\hat{v}}{\hat{\omega}}\cos\theta - \frac{\hat{v}}{\hat{\omega}}\cos(\theta + \hat{\omega}\Delta t)$$
$$\theta' = \theta + \hat{\omega}\Delta t + \hat{\gamma}\Delta t$$

![[Velocidad - modelo 3er parametro.png]]
*El término $\hat{\gamma}\Delta t$ contempla la rotación final, slide 37.*

## 5. Derivación: parámetros del círculo

Dadas dos poses $x_{t-1} = (x, y, \theta)^T$ y $x_t = (x', y', \theta')^T$, el ICC está en:

$$\begin{pmatrix} x^* \\ y^* \end{pmatrix} = \begin{pmatrix} x \\ y \end{pmatrix} + \begin{pmatrix} -\lambda\sin\theta \\ \lambda\cos\theta \end{pmatrix}$$

con $\lambda = R$ (distancia al ICC, ortogonal a la orientación inicial). Combinando con la condición de que $x_t$ esté a la misma distancia del ICC:

$$\begin{pmatrix} x^* \\ y^* \end{pmatrix} = \begin{pmatrix} \tfrac{x + x'}{2} + \mu(y - y') \\ \tfrac{y + y'}{2} + \mu(x' - x) \end{pmatrix}$$

![[Velocidad - ICC setup.png]]
*El ICC está sobre la línea ortogonal a la orientación inicial; $\lambda$ es la distancia constante, slide 38.*

Resolviendo para $\mu$:
$$\mu = \frac{1}{2}\frac{(x - x')\cos\theta + (y - y')\sin\theta}{(y - y')\cos\theta - (x - x')\sin\theta}$$

![[Velocidad - mu solucion.png]]
*Despeje de $\mu$ a partir de las dos poses y la orientación inicial, slide 41.*

Y entonces el radio y el cambio angular son:
$$r^* = \sqrt{(x' - x^*)^2 + (y' - y^*)^2}$$
$$\Delta\theta = \text{atan2}(y' - y^*, x' - x^*) - \text{atan2}(y - y^*, x - x^*)$$

![[Velocidad - r delta theta.png]]
*Cálculo de $r^*$ y $\Delta\theta$ desde las poses, slide 42.*

Las velocidades correspondientes son:
$$v = \frac{\Delta\theta}{\Delta t}r^* \qquad \omega = \frac{\Delta\theta}{\Delta t}$$

## 6. Algoritmo `motion_model_velocity`

Cálculo de la probabilidad $p(x_t \mid x_{t-1}, u_t)$:

```
motion_model_velocity(x_t, u_t, x_{t-1}):
  μ  = (1/2) · [(x - x')cos θ + (y - y')sin θ]
            / [(y - y')cos θ - (x - x')sin θ]
  x* = (x + x')/2 + μ(y - y')
  y* = (y + y')/2 + μ(x' - x)
  r* = sqrt((x - x*)² + (y - y*)²)
  Δθ = atan2(y' - y*, x' - x*) - atan2(y - y*, x - x*)
  v̂  = Δθ/Δt · r*
  ω̂  = Δθ/Δt
  γ̂  = (θ' - θ)/Δt - ω̂

  return prob(v - v̂, α₁v² + α₂ω²) ·
         prob(ω - ω̂, α₃v² + α₄ω²) ·
         prob(γ̂,     α₅v² + α₆ω²)
```

![[Velocidad - motion model algoritmo.png]]
*Algoritmo `motion_model_velocity`, slide 44.*

## 7. Algoritmo `sample_motion_model_velocity`

Para muestrear $x_t$ dado $u_t, x_{t-1}$:

```
sample_motion_model_velocity(u_t, x_{t-1}):
  v̂ = v + sample(α₁v² + α₂ω²)
  ω̂ = ω + sample(α₃v² + α₄ω²)
  γ̂ =     sample(α₅v² + α₆ω²)

  x' = x - (v̂/ω̂) sin θ + (v̂/ω̂) sin(θ + ω̂ Δt)
  y' = y + (v̂/ω̂) cos θ - (v̂/ω̂) cos(θ + ω̂ Δt)
  θ' = θ + ω̂ Δt + γ̂ Δt

  return (x', y', θ')^T
```

![[Velocidad - sample motion model algoritmo.png]]
*Algoritmo `sample_motion_model_velocity`, slide 45.*

## 8. Ejemplos del modelo

Como con odometría, la distribución $p(x_t \mid u_t, x_{t-1})$ tiene forma de medialuna, ahora más curvada porque la trayectoria es un arco:

![[Velocidad - ejemplos sampling.png]]
*Distribuciones $p(x' \mid u, x)$ vs muestras correspondientes para distintos ruidos en el modelo de velocidad, slide 46.*

## 9. Modelo consistente con mapas

Igual que con odometría, el modelo $p(x_t \mid u_t, x_{t-1})$ por sí solo ignora los obstáculos del entorno: una muestra puede caer dentro de una pared. Si conocemos un mapa $m$, la aproximación es la misma:

$$p(x' \mid u, x, m) = \eta \, p(x' \mid m) \, p(x' \mid u, x)$$

donde $p(x' \mid m)$ es 0 si la pose es ocupada y constante si es libre. Para la imagen y el detalle ver [[Modelo de Movimiento (Odometría)#12. Modelo consistente con mapas|Modelo de Movimiento (Odometría) → sec. 12]] — la slide 47 reutiliza la misma figura que la slide 31.

## 10. Variantes y conexiones
- [[Modelo de Movimiento (Odometría)]] — modelo paralelo cuando hay encoders.
- [[Muestreo de Distribuciones]] — algoritmos `prob` y `sample` que `motion_model_velocity` y `sample_motion_model_velocity` invocan.
- [[MCL - Filtro de Partículas]] — usa `sample_motion_model_velocity` cuando el robot reporta velocidades (forward-ref, M5).
- [[Filtro de Kalman]] / [[EKF]] — la versión linealizada se usa como modelo de transición (forward-ref, M5).

## Fuentes
- `Raw/Diapositivas/Teoricas/06-modelos-de-movimiento_con_modelo_velocidad-3.pdf`
  - slide 7 → 1. Cuándo usar este modelo
  - slide 34 → 2. Setup: control como velocidad
  - slide 35 → 3. Modelo de ruido (primera versión)
  - slides 36–37 → 4. Modelo extendido con tercer parámetro
  - slides 38–42 → 5. Derivación: parámetros del círculo
  - slide 44 → 6. Algoritmo `motion_model_velocity` (delega `prob` a [[Muestreo de Distribuciones]])
  - slide 45 → 7. Algoritmo `sample_motion_model_velocity` (delega `sample` a [[Muestreo de Distribuciones]])
  - slide 46 → 8. Ejemplos del modelo
  - slide 47 → 9. Modelo consistente con mapas