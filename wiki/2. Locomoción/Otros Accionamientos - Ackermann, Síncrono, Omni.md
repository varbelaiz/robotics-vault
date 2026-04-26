---
modulo: 2. Locomoción
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/03-locomocion-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Locomoción]] | [[Home|← Inicio]]

# Otros Accionamientos: Ackermann, Síncrono, Omnidireccional

> Más allá del diferencial: dirección tipo automóvil, accionamiento síncrono, ruedas omnidireccionales y mecanum, y vehículos de configuración especial.

## Prerequisitos
- [[Cinemática del Robot Diferencial]] — la idea de ICC y la lógica de derivación se reutilizan.
- [[Locomoción y Tipos de Accionamientos]] — diferenciar holonómicos de no-holonómicos.

## 1. Dirección tipo Ackermann  *(Teóricas 03-locomocion, slides 24–25)*
La **dirección Ackermann** es la del automóvil convencional: dos ruedas delanteras directrices que giran ángulos distintos para que todas las ruedas pivoten alrededor del mismo ICC.

![[Ackermann - setup.png]]
*Geometría Ackermann: las dos ruedas delanteras giran ángulos distintos para compartir un mismo centro de curvatura, slide 24.*

Las ecuaciones son análogas al diferencial. Con $d$ la distancia entre eje delantero y trasero, $\phi$ el ángulo de la rueda directriz virtual y $V_r, V_l$ las velocidades de las ruedas exteriores e interiores:

$$R = \frac{d}{\tan\phi}$$
$$\omega = \frac{V_r - V_l}{l} \qquad R = \frac{l}{2}\frac{V_l + V_r}{V_r - V_l}$$
$$ICC = [\,x - R\sin\theta,\; y + R\cos\theta\,]$$

![[Ackermann - formulas.png]]
*Fórmulas del modelo Ackermann, slide 25.*

> [!info] No-holonómico
> Igual que el diferencial, Ackermann es **no-holonómico**: no puede moverse lateralmente (no se puede estacionar en paralelo sin maniobras).

## 2. Accionamiento síncrono  *(Teóricas 03-locomocion, slides 26–27)*
En un robot **síncrono**, todas las ruedas giran al unísono y rotan al unísono — un único motor controla la velocidad lineal $V(t)$ y otro controla la orientación $\omega(t)$. La trayectoria es:

$$x(t) = \int_0^t V(t)\cos[\theta(t)]\,dt$$
$$y(t) = \int_0^t V(t)\sin[\theta(t)]\,dt$$
$$\theta(t) = \int_0^t \omega(t)\,dt$$

![[Sincrono - setup.png]]
*Configuración síncrona: tres ruedas que giran y rotan en sincronía, slide 26.*

Un ejemplo histórico es el **XR4000** (Nomadics), con cuatro ruedas síncronas:

![[Sincrono - XR4000.png]]
*XR4000 con accionamiento síncrono de cuatro ruedas, slide 27.*

## 3. Ruedas Mecanum  *(Teóricas 03-locomocion, slide 28)*
Las **ruedas Mecanum** tienen rodillos a 45° en su circunferencia. Combinando las velocidades de las cuatro ruedas se puede lograr movimiento omnidireccional:

$$v_y = (v_0 + v_1 + v_2 + v_3)/4$$
$$v_x = (v_0 - v_1 + v_2 - v_3)/4$$
$$v_\theta = (v_0 + v_1 - v_2 - v_3)/4$$
$$v_{\text{error}} = (v_0 - v_1 - v_2 + v_3)/4$$

El cuarto término ($v_{\text{error}}$) debe ser cero en operación válida; es una métrica de patinaje.

![[Mecanum - ecuaciones.png]]
*Robot con ruedas Mecanum y sus ecuaciones cinemáticas, slide 28.*

## 4. Plataforma Kuka OmniRob  *(Teóricas 03-locomocion, slide 30)*
Plataforma comercial holonómica con ruedas omni:

![[Kuka OmniRob.png]]
*Plataforma Kuka OmniRob con accionamiento omnidireccional, slide 30.*

## 5. Omnibot de 3 ruedas  *(Teóricas 03-locomocion, slide 31)*
Configuración de tres ruedas omni a 120°. La cinemática es:

$${}^{I}\dot{\boldsymbol{\xi}} = R(\theta)^{-1}\,J_{1f}^{-1}\,J_2\,\dot{\boldsymbol{\varphi}}$$

con $J_{1f}$ una matriz que codifica la geometría de las ruedas:

$$J_{1f} = \begin{bmatrix} \sin\tfrac{\pi}{3} & -\cos\tfrac{\pi}{3} & -l \\ 0 & -\cos\pi & -l \\ \sin(-\tfrac{\pi}{3}) & -\cos(-\tfrac{\pi}{3}) & -l \end{bmatrix}$$

![[Omnibot - 3 ruedas.png]]
*Omnibot de 3 ruedas omnidireccionales a 120°, slide 31.*

## 6. Otros tipos de vehículos  *(Teóricas 03-locomocion, slide 33)*
Más allá de las ruedas, otros tipos de plataformas con cinemática propia:

- **Oruga** — tracción por orugas (tanques pequeños, exploración).
- **Anfibio** — opera en agua y tierra.
- **Hexápodo** — robot de 6 patas, alto DOF.
- **Bípedo** — humanoides (ASIMO, Atlas).
- **Aéreo** — drones cuadricópteros.

![[Otros vehiculos.png]]
*Oruga, anfibio, hexápodo, bípedo, drone aéreo, slide 33.*

## Fuentes
- `Raw/Diapositivas/Teoricas/03-locomocion-3.pdf` — slides 24–33.
