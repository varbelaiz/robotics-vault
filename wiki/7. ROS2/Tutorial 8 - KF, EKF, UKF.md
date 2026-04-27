---
modulo: 7. ROS2
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 8_ KF, EKF, UKF.pdf
ultima_actualizacion: 2026-04-27
---

> [[ROS2|← ROS2]] | [[Robotica|← Inicio]]

# Tutorial 8 — KF, EKF, UKF

> Aterriza los **filtros gaussianos paramétricos**: arranca con un truco numérico clave para MCL (log-likelihoods + max-shift), después deriva el [[Filtro de Kalman]] paso a paso desde la dinámica $x_t = A_t x_{t-1} + B_t u_t + \varepsilon_t$, extiende a [[EKF]] vía Taylor + Jacobianos, y termina con [[UKF]] (sigma points) y consideraciones de integración en ROS2 para el TP.

## Objetivo

Que cada estudiante pueda:

1. Estabilizar numéricamente la actualización de pesos en MCL usando log-likelihoods.
2. Construir el modelo de un sistema en forma estado-espacio: $x_t = A_t x_{t-1} + B_t u_t + \varepsilon_t$, $z_t = C_t x_t + \delta_t$.
3. Implementar predict/correct del **KF** lineal.
4. Linealizar un sistema no-lineal con Jacobianos para usar **EKF**.
5. Decidir cuándo elegir UKF sobre EKF.
6. Integrar el filtro en ROS2 (TP4) eligiendo entre dos arquitecturas (odometría rápida vs. paralelo).

## Conceptos que se aplican
- [[Filtro de Bayes]] — base teórica.
- [[Filtro de Kalman]] / [[EKF]] / [[UKF]] — los tres filtros del tutorial.
- [[Gaussiana Multivariada]] — el belief paramétrico $(\mu, \Sigma)$.
- [[Matriz Jacobiana]] — linealización del EKF.
- [[Puntos Sigma]] — UKF.

## Estructura del tutorial

| Slides | Sección |
|---|---|
| 2–15 | Recap TP3: trick numérico log-likelihoods |
| 16–20 | KF — visión general |
| 21–52 | KF predict, derivado paso a paso desde $x_t = A_t x_{t-1} + B_t u_t + \varepsilon_t$ |
| 53–59 | KF correct con ejemplo numérico |
| 60–63 | EKF — Taylor + Jacobianos |
| 64–75 | EKF — ejercicio robot 2D con yaw incierto (problema de la linealización) |
| 76–77 | UKF + comparación EKF vs UKF |
| 78–79 | TP4: integración en ROS2 |

## 1. Truco numérico: log-likelihoods en MCL

![[Tutorial 8 - likelihood field scan.png]]
*Recap del [[Tutorial 7 - Filtro de Partículas|Tutorial 7]]: el robot evalúa cada haz del lidar contra el likelihood field; cada haz da una verosimilitud, y el peso de la partícula es el producto de todas. Slide 4.*

Con $K \approx 360$ haces de lidar, el peso de cada partícula es el **producto** de $K$ likelihoods:

$$w^{[i]} = \prod_{j=1}^{K} p(z_j \mid x^{[i]})$$

Con likelihoods típicas $\sim 10^{-3}$, después de 360 multiplicaciones llegamos a $10^{-1080}$ — **underflow numérico** masivo.

![[Tutorial 8 - log likelihood formula.png]]
*Solución: usar el logaritmo. $\log w^{[i]} = \sum_j \log p(z_j \mid x^{[i]})$. Con $\varepsilon$ para evitar $\log(0)$: $\log w^{[i]} = \sum_j \log(p(z_j \mid x^{[i]}) + \varepsilon)$, slide 13.*

Pero al volver a exponenciar para resamplear, $\exp(-1080) = 0$ otra vez. La solución completa es **shiftear** por el máximo log-likelihood antes de exponenciar:

![[Tutorial 8 - trick exp log max.png]]
*El truco: $w^{[i]} = \exp\!\left(\log w^{[i]} - \max_j \log w^{[j]}\right)$. La partícula con mayor log-likelihood termina con peso 1, las demás con valores entre 0 y 1. La normalización posterior es estable, slide 15.*

> [!warning] Por qué shift por max y no por la suma
> Restando el máximo, el mayor exponente queda en 0 → no overflow. Si restáramos la media o algún otro valor, la partícula más probable podría tener un exponente positivo grande y desbordar.

## 2. Filtro de Kalman — Visión general

![[Tutorial 8 - KF predict correct loop.png]]
*El KF mantiene el belief como gaussiana $(\mu, \Sigma)$: predict (propaga la dinámica) → measure → correct (incorpora la medición). Misma estructura que el filtro de Bayes pero con representación paramétrica, slide 18.*

> [!info] KF vs filtro de partículas — la misma estructura
> El filtro de partículas hace lo mismo (predict → measure → resample). La diferencia es la representación: KF usa $(\mu, \Sigma)$ asumiendo gaussiana; el filtro de partículas usa muestras sin suposición de forma.

## 3. Modelo lineal-gaussiano

El KF asume:

$$x_t = A_t\, x_{t-1} + B_t\, u_t + \varepsilon_t, \quad \varepsilon_t \sim \mathcal{N}(0, Q_t)$$
$$z_t = C_t\, x_t + \delta_t, \quad \delta_t \sim \mathcal{N}(0, R_t)$$

### Ejemplo: vehículo con dinámica lineal

Vehículo con posición $x_t$, velocidad $\dot{x}_t$, aceleración $\ddot{x}_t$ aleatoria gaussiana de media 0 y varianza 1.

![[Tutorial 8 - vector estado vs ruido.png]]
*Decisión clave: ¿qué entra al **vector de estado** y qué entra al **ruido**? Si la aceleración es estocástica y no la controlamos, va al ruido $\varepsilon_t$. Estado mínimo Markoviano: $(x_t, \dot{x}_t)^T$. Slide 33.*

> [!info] Markoviano = el estado captura todo lo necesario
> Si la aceleración fuera determinística (la controlamos), iría al control $u_t$. Como es estocástica e influye en el siguiente estado, debe estar en el estado o en el ruido.

### Derivación del paso de predicción

Partiendo de $x_t = A_t x_{t-1} + B_t u_t + \varepsilon_t$, tomando esperanza y varianza:

- $\mathbb{E}[x_t] = A_t \mathbb{E}[x_{t-1}] + B_t u_t$ → $\bar{\mu}_t = A_t \mu_{t-1} + B_t u_t$
- $\text{Var}(x_t) = A_t \Sigma_{t-1} A_t^T + Q_t$ → $\bar{\Sigma}_t = A_t \Sigma_{t-1} A_t^T + Q_t$

Para nuestro vehículo con $\Delta t = 1$, $\ddot{x}_t \sim \mathcal{N}(0, 1)$:

![[Tutorial 8 - matrices A y Q dt.png]]
*Matrices finales: $A = \begin{pmatrix} 1 & \Delta t \\ 0 & 1 \end{pmatrix}$, $Q = \sigma^2 \begin{pmatrix} \tfrac{1}{4}\Delta t^4 & \tfrac{1}{2}\Delta t^3 \\ \tfrac{1}{2}\Delta t^3 & \Delta t^2 \end{pmatrix}$, slide 50.*

> [!info] $Q$ acopla posición y velocidad
> El ruido en la aceleración impacta a posición ($\Delta t^2/2$) y velocidad ($\Delta t$). Por eso $Q$ no es diagonal — los ruidos de los dos estados están **correlacionados** vía la misma fuente.

## 4. Algoritmo del Kalman Filter

![[Tutorial 8 - algoritmo Kalman filter.png]]
*Pseudocódigo completo: predict + correct. La ganancia de Kalman $K_t$ pondera cuánto confiar en la medición vs. en la predicción, slide 52.*

```
Algoritmo Kalman_filter(μ_{t-1}, Σ_{t-1}, u_t, z_t):
    # PREDICCIÓN
    μ̄_t = A_t μ_{t-1} + B_t u_t
    Σ̄_t = A_t Σ_{t-1} A_t^T + Q_t

    # CORRECCIÓN
    K_t = Σ̄_t C_t^T (C_t Σ̄_t C_t^T + R_t)^(-1)
    μ_t = μ̄_t + K_t (z_t − C_t μ̄_t)
    Σ_t = (I − K_t C_t) Σ̄_t

    return μ_t, Σ_t
```

### Derivación del paso de corrección

![[Tutorial 8 - derivacion KF update.png]]
*La corrección sale de combinar la prior $\mathcal{N}(\bar{\mu}_t, \bar{\Sigma}_t)$ con la likelihood $\mathcal{N}(z_t; C_t x_t, R_t)$ y aplicar Bayes. El producto de gaussianas es una gaussiana cuya media y covarianza se reescriben como $\mu_t = \bar{\mu}_t + K_t(z_t - C_t \bar{\mu}_t)$, $\Sigma_t = (I - K_t C_t) \bar{\Sigma}_t$, slide 59.*

> [!info] La ganancia de Kalman como factor de confianza
> Si $R_t \to \infty$ (medición ruidosísima) → $K_t \to 0$ → ignoramos la medición. Si $R_t \to 0$ (medición perfecta) → $K_t \to C_t^{-1}$ → confiamos 100% en la medición. $K_t$ balancea automáticamente.

## 5. EKF — sistemas no lineales

KF asume linealidad. Cuando la dinámica o el sensor son no-lineales, $x_t = g(u_t, x_{t-1})$ y $z_t = h(x_t)$:

![[Tutorial 8 - EKF linearizacion visual.png]]
*Pasar una gaussiana por una función no-lineal **no** da una gaussiana. EKF aproxima la salida con una gaussiana, linealizando la función con Taylor de primer orden alrededor de la media, slide 60.*

![[Tutorial 8 - EKF Taylor jacobianos.png]]
*Linealización: $g(u_t, x_{t-1}) \approx g(u_t, \mu_{t-1}) + G_t (x_{t-1} - \mu_{t-1})$ donde $G_t = \partial g / \partial x_{t-1}$ es la **matriz Jacobiana** de $g$, slide 62.*

![[Tutorial 8 - algoritmo EKF.png]]
*Algoritmo EKF: idéntico al KF pero reemplazando $A_t \to G_t$ (Jacobiana de $g$), $C_t \to H_t$ (Jacobiana de $h$), y usando $g(u_t, \mu_{t-1})$, $h(\bar{\mu}_t)$ donde el KF usaría las matrices, slide 63.*

```
Algoritmo Extended_Kalman_filter(μ_{t-1}, Σ_{t-1}, u_t, z_t):
    # PREDICCIÓN
    μ̄_t = g(u_t, μ_{t-1})
    Σ̄_t = G_t Σ_{t-1} G_t^T + Q_t

    # CORRECCIÓN
    K_t = Σ̄_t H_t^T (H_t Σ̄_t H_t^T + R_t)^(-1)
    μ_t = μ̄_t + K_t (z_t − h(μ̄_t))
    Σ_t = (I − K_t H_t) Σ̄_t

    return μ_t, Σ_t
```

### Cuándo se rompe la linealización

Robot 2D con $\mu = (x, y, \theta)$ y $\Sigma$ con incertidumbre **alta en $\theta$** (10000) y **baja en $x, y$** (0.01):

![[Tutorial 8 - robot 2D ecuaciones.png]]
*Ecuaciones de movimiento: $x_{t+1} = x + \cos\theta$, $y_{t+1} = y + \sin\theta$. No-lineales en $\theta$, slide 65.*

![[Tutorial 8 - scatter circular yaw.png]]
*Con $\theta$ uniforme, 1000 muestras de la posición tras moverse 1 unidad caen en un **círculo** de radio 1 — la verdadera distribución NO es gaussiana, slide 66.*

![[Tutorial 8 - medicion elipse final.png]]
*El EKF aproxima esa distribución circular con una elipse linealizada (azul/verde) — pierde información catastróficamente. La medición de $x$ tampoco salva la situación, porque la prior linealizada ya está mal, slide 75.*

> [!warning] EKF asume que la incertidumbre es chica respecto a la curvatura
> La linealización de Taylor sólo es buena cerca del punto de expansión. Si $\Sigma$ es grande comparado con cómo curva la función $g$, el EKF da resultados catastróficamente malos.

## 6. UKF — Unscented Kalman Filter

UKF resuelve el problema del EKF muestreando **deterministically** un set de **sigma points** alrededor de $\mu$, propagándolos por la función no-lineal real $g$, y reconstruyendo media + covarianza pesando los puntos transformados:

![[Tutorial 8 - UKF sigma points vs EKF.png]]
*Tres aproximaciones del paso $y = f(x)$: izq) sampling masivo (verdad de campo), centro) EKF (linealización tangente), der) UT con ~5 sigma points y reconstrucción ponderada. UT preserva mejor la "verdadera" media y covarianza, slide 76.*

Detalle teórico en [[UKF]] y [[Puntos Sigma]] (M5).

### EKF vs. UKF

![[Tutorial 8 - tabla EKF vs UKF.png]]
*Tabla comparativa: EKF eficiente y bueno para sistemas ligeramente no-lineales; UKF más robusto para sistemas fuertemente no-lineales o con mucha incertidumbre, a costa de más cómputo, slide 77.*

| Caso | EKF | UKF |
|---|---|---|
| Sistemas ligeramente no-lineales | Bueno | Bueno |
| Sistemas fuertemente no-lineales | Difícil | Mejor performance |
| Sistemas ruidosos / multidim. / complejos | Menos confiable | Más robusto |
| Cantidad de cómputo | Eficiente | Puede ser más pesado |

## 7. Integración en ROS2 (TP4)

![[Tutorial 8 - TP4 paralelo opciones.png]]
*Dos arquitecturas para integrar el filtro en ROS2: izq) odometría rápida (acumular movimientos hasta que llegue medición, hacer predict + correct juntos), der) paralelo (predict cuando llega odometría, correct cuando llega medición, publicar belief tras cada update). El paralelo requiere cuidado con race conditions — recomendable trabajar sobre una copia del belief, slide 79.*

| | Odometría Rápida | Paralelo |
|---|---|---|
| **Cuándo predict** | Acumulado, en el ciclo de medición | En cada mensaje de odometría |
| **Cuándo publish** | Una vez por ciclo de medición | Tras cada update |
| **Race conditions** | No hay — flujo serial | Sí — usar copia del belief |
| **Latencia del belief** | Mayor (espera medición) | Menor (publish continuo) |

## Conexiones
- [[Tutorial 7 - Filtro de Partículas]] — tutorial anterior (filtro no paramétrico).
- [[Filtro de Kalman]] / [[EKF]] / [[UKF]] — teoría.
- [[Gaussiana Multivariada]] — la representación del belief.
- [[Matriz Jacobiana]] — linealización del EKF.
- [[Puntos Sigma]] — UKF.
- [[TP3 - Filtros de Partículas]] — el truco de log-likelihoods aplica acá; el TP4 (mencionado en slide 79) es donde se integra el filtro en ROS2.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 8_ KF, EKF, UKF.pdf`
  - slides 2–15 → 1. Truco numérico: log-likelihoods en MCL
  - slides 16–20 → 2. Filtro de Kalman — Visión general
  - slides 21–50 → 3. Modelo lineal-gaussiano (derivación predict)
  - slides 52–59 → 4. Algoritmo del KF + derivación correct
  - slides 60–63 → 5. EKF — sistemas no lineales
  - slides 64–75 → 5.1 Cuándo se rompe la linealización (robot 2D)
  - slides 76–77 → 6. UKF + comparación
  - slides 78–79 → 7. Integración en ROS2 (TP4)
