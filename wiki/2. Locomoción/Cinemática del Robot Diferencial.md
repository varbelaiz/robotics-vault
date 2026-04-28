---
modulo: 2. Locomoción
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/03-locomocion-3.pdf
  - Raw/Diapositivas/Teoricas/06-modelos-de-movimiento_con_modelo_velocidad-3.pdf
ultima_actualizacion: 2026-04-27
---

> [[Locomoción|← Locomoción]] | [[Robotica|← Inicio]]

# Cinemática del Robot Diferencial

> Modelo cinemático directo del accionamiento más común: dadas las velocidades de giro de cada rueda, calcular la velocidad del robot en el marco global.

## Prerequisitos
- [[Rotaciones]] — matrices ortogonales 2D.
- [[Locomoción y Tipos de Accionamientos]] — qué es un robot diferencial.

## 1. Cinemática vs dinámica

- **Cinemática**: rama de la mecánica que describe el movimiento **sin considerar las causas** (las fuerzas). Estudia la trayectoria en función del tiempo usando velocidades y aceleraciones.
- **Dinámica**: estudia la **relación entre fuerzas y los efectos** sobre el movimiento del cuerpo.

En este módulo trabajamos con **modelos cinemáticos** — basta con saber cómo se mueve el robot en respuesta a las velocidades de las ruedas; el porqué (torques, fricciones) queda fuera.

## 2. Pose 2D del robot

### Del 3D al planar

La configuración general de un robot en 3D se describe con **6 parámetros**: las 3 coordenadas cartesianas $(x, y, z)$ más 3 ángulos. Los ángulos pueden ser **intrínsecos** (rotaciones sobre los ejes locales del cuerpo, p. ej. Euler clásicos) o **extrínsecos** (rotaciones sobre los ejes globales fijos, p. ej. Roll-Pitch-Yaw).

![[Coordenadas - 3D Euler RPY.png]]
*Configuración 3D: misma rotación con ángulos intrínsecos (Euler) vs extrínsecos (RPY), PDF 06 slide 5.*

> [!info] Cuándo importa la distinción
> Para robots aéreos o brazos manipuladores, el orden de las rotaciones cambia el resultado — por eso conviene representar la orientación con [[Cuaterniones]] o matrices, no con tuplas de ángulos. Ver [[Rotaciones]] (M1) para la justificación detallada.

### Restricción a la superficie plana

Por simplicidad, este módulo (y todo el curso de robótica móvil) trabaja con robots que operan sobre **una superficie plana**. El espacio de estados se reduce de 6 a **3 dimensiones** $(x, y, \theta)$:

![[Coordenadas - planar xytheta.png]]
*Pose planar de un robot móvil: dos coordenadas y un ángulo, PDF 06 slide 6.*

### Notación del curso

Para un robot que opera sobre una superficie plana, su **pose** se describe con tres parámetros:

$${}^{I}\boldsymbol{\xi} = \begin{bmatrix} x \\ y \\ \theta \end{bmatrix}$$

donde $(x, y)$ es la posición del punto de referencia $\boldsymbol{p}$ del chasis del robot en el marco de referencia global $\{X_I, Y_I\}$, y $\theta$ es la orientación del marco local del robot $\{X_R, Y_R\}$ respecto del global.

![[Cinematica - pose 2D.png]]
*Marco global $\{X_I, Y_I\}$, marco local del robot $\{X_R, Y_R\}$ y la pose ${}^{I}\boldsymbol{\xi} = (x, y, \theta)$, PDF 03 slide 10.*

## 3. Mapeo entre marcos: matriz de rotación

Para describir el movimiento del robot en términos de sus ruedas, hay que **mapear velocidades del marco global al marco local del robot**. Esto se hace con la matriz de rotación ortogonal:

$${}^{R}\boldsymbol{R}_I = R(\theta) = \begin{bmatrix} \cos\theta & \sin\theta & 0 \\ -\sin\theta & \cos\theta & 0 \\ 0 & 0 & 1 \end{bmatrix}$$

El mapeo de velocidades es:

$${}^{R}\dot{\boldsymbol{\xi}} = {}^{R}\boldsymbol{R}_I \, {}^{I}\dot{\boldsymbol{\xi}}$$

![[Cinematica - matriz rotacion.png]]
*La matriz $R(\theta)$ mapea velocidades global → local, slide 12.*

> [!info] Caso $\theta = \pi/2$
> Cuando el robot apunta hacia $+Y_I$ (es decir, $\theta = \pi/2$), $\dot{x}_R = \dot{y}$ y $\dot{y}_R = -\dot{x}$. La matriz se evalúa a la rotación de 90°.

## 4. Cinemática directa: planteo

El modelo de cinemática directa de un robot diferencial es:

$${}^{I}\dot{\boldsymbol{\xi}} = \begin{bmatrix} \dot{x} \\ \dot{y} \\ \dot{\theta} \end{bmatrix} = f(l, r, \theta, \dot{\varphi}_1, \dot{\varphi}_2)$$

donde:
- $l/2$ es la distancia de cada rueda al punto $\boldsymbol{p}$ (centro del eje entre ruedas).
- $r$ es el radio de las ruedas.
- $\dot{\varphi}_1$, $\dot{\varphi}_2$ son las velocidades de giro de las ruedas derecha e izquierda.

La estrategia es calcular primero las velocidades en el marco local ${}^{R}\dot{\boldsymbol{\xi}}$ y luego transformar al global con ${}^{I}\dot{\boldsymbol{\xi}} = {}^{R}\boldsymbol{R}_I^{-1} \, {}^{R}\dot{\boldsymbol{\xi}}$.

![[Cinematica - directa diferencial setup.png]]
*Setup del problema con las velocidades $\dot{\varphi}_1, \dot{\varphi}_2$, el radio $r$ y la separación $l$, slide 14.*

## 5. Derivación: velocidad de traslación

La contribución de cada rueda a la velocidad de traslación en dirección $+X_R$ (hacia adelante) es la mitad de su velocidad lineal, porque $\boldsymbol{p}$ está en el medio entre las dos ruedas:

$$\dot{x}_{r1} = \tfrac{1}{2}r\dot{\varphi}_1 \qquad \dot{x}_{r2} = \tfrac{1}{2}r\dot{\varphi}_2$$

Sumando:
$$\dot{x}_R = \dot{x}_{r1} + \dot{x}_{r2} = \tfrac{1}{2}r\dot{\varphi}_1 + \tfrac{1}{2}r\dot{\varphi}_2$$

La componente lateral $\dot{y}_R$ es **siempre cero**, porque ninguna de las dos ruedas (orientadas hacia adelante) puede contribuir al movimiento lateral en el marco del robot. Esta es la **restricción no-holonómica** del diferencial.

## 6. Derivación: velocidad rotacional

Cuando una rueda gira sola, el robot pivota alrededor de la otra. El giro hacia adelante de la rueda derecha (índice 1) genera rotación antihoraria en $\boldsymbol{p}$, alrededor de un círculo de radio $l$:

$$\omega_1 = \frac{r\dot{\varphi}_1}{l}$$

La rueda izquierda (índice 2) genera rotación horaria:
$$\omega_2 = \frac{-r\dot{\varphi}_2}{l}$$

La velocidad angular total es la suma:
$$\dot{\theta}_R = \omega_1 + \omega_2 = \frac{r\dot{\varphi}_1}{l} + \frac{-r\dot{\varphi}_2}{l}$$

![[Cinematica - rotacional.png]]
*Contribución rotacional de cada rueda, slide 17.*

## 7. Resultado final

Las velocidades en el marco local del robot:

$${}^{R}\dot{\boldsymbol{\xi}} = \begin{bmatrix} \dot{x} \\ \dot{y} \\ \dot{\theta} \end{bmatrix} = \begin{bmatrix} \tfrac{r}{2}(\dot{\varphi}_1 + \dot{\varphi}_2) \\ 0 \\ \tfrac{r}{l}(\dot{\varphi}_1 - \dot{\varphi}_2) \end{bmatrix}$$

Y en el marco global:
$${}^{I}\dot{\boldsymbol{\xi}} = {}^{R}\boldsymbol{R}_I^{-1} \, {}^{R}\dot{\boldsymbol{\xi}}$$

![[Cinematica - ecuaciones finales.png]]
*Las velocidades del robot diferencial en el marco local, slide 18.*

## 8. Trayectorias e ICC

Las trayectorias resultantes son **(instantáneamente) círculos**.

![[Cinematica - trayectorias circulares.png]]
*Las trayectorias del diferencial son círculos instantáneos, slide 19.*

Para que se cumpla la **condición de rodamiento** (las ruedas no patinan), todas las ruedas deben girar alrededor del **mismo punto**. Este punto se llama **Centro de Curvatura Instantáneo (ICC)**.

![[ICC - rodamiento.png]]
*El ICC: punto único alrededor del cual giran todas las ruedas, slide 20.*

## 9. Cinemática directa con ICC

Definiendo $V_l = r\dot{\varphi}_2$, $V_r = r\dot{\varphi}_1$ y $R$ como la distancia desde el robot al ICC:

$$\omega(R + l/2) = V_r$$
$$\omega(R - l/2) = V_l$$

Despejando:
$$\omega = \frac{V_r - V_l}{l} \qquad R = \frac{l}{2}\frac{V_l + V_r}{V_r - V_l}$$

La posición del ICC en el marco global:
$$ICC = [\,x - R\sin\theta,\; y + R\cos\theta\,]$$

![[Diferencial - ICC formulas.png]]
*Fórmulas del ICC para el accionamiento diferencial, slide 22.*

Integrando el movimiento alrededor del ICC con velocidad angular $\omega$:

$$\begin{bmatrix} x' \\ y' \\ \theta' \end{bmatrix} = \begin{bmatrix} \cos(\omega\delta t) & -\sin(\omega\delta t) & 0 \\ \sin(\omega\delta t) & \cos(\omega\delta t) & 0 \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} x - ICC_x \\ y - ICC_y \\ \theta \end{bmatrix} + \begin{bmatrix} ICC_x \\ ICC_y \\ \omega\delta t \end{bmatrix}$$

Y la trayectoria en el tiempo es:

$$x(t) = \tfrac{1}{2}\int_0^t [v_r(t) + v_l(t)]\cos[\theta(t)]\,dt$$
$$y(t) = \tfrac{1}{2}\int_0^t [v_r(t) + v_l(t)]\sin[\theta(t)]\,dt$$
$$\theta(t) = \tfrac{1}{l}\int_0^t [v_r(t) - v_l(t)]\,dt$$

![[Diferencial - integrales.png]]
*Movimiento alrededor del ICC y la trayectoria integrada, slide 23.*

## 10. Variantes y conexiones
- [[Otros Accionamientos - Ackermann, Síncrono, Omni]] — el ICC también aplica a Ackermann.
- [[Modelo de Movimiento (Velocidad)]] — el modelo probabilístico que toma $u = (v, \omega)$ está construido sobre estas mismas ecuaciones.
- [[Modelo de Movimiento (Odometría)]] — usa las mismas integrales para estimar la pose desde encoders.

## Fuentes
- `Raw/Diapositivas/Teoricas/03-locomocion-3.pdf`
  - slide 9 → 1. Cinemática vs dinámica
  - slides 10–11 → 2. Pose 2D del robot — *Notación del curso*
  - slides 12–13 → 3. Mapeo entre marcos: matriz de rotación
  - slide 14 → 4. Cinemática directa: planteo
  - slides 15–16 → 5. Derivación: velocidad de traslación
  - slide 17 → 6. Derivación: velocidad rotacional
  - slide 18 → 7. Resultado final
  - slides 19–20 → 8. Trayectorias e ICC
  - slides 22–23 → 9. Cinemática directa con ICC
- `Raw/Diapositivas/Teoricas/06-modelos-de-movimiento_con_modelo_velocidad-3.pdf`
  - slide 5 → 2. Pose 2D del robot — *Del 3D al planar*
  - slide 6 → 2. Pose 2D del robot — *Restricción a la superficie plana*