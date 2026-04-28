---
modulo: 2. Locomoción
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/03-locomocion-3.pdf
ultima_actualizacion: 2026-04-28
---

> [[Locomoción|← Locomoción]] | [[Robotica|← Inicio]]

# Odometría

> Estimación del movimiento del robot integrando las velocidades de las ruedas en el tiempo. Caso particular de **dead reckoning** ("deduced reckoning"): cálculo de la posición a partir de velocidades, sin referencias externas.

## Prerequisitos
- [[Cinemática del Robot Diferencial]] — odometría es la integración temporal de la cinemática.
- [[Encoders Ópticos]] — sensor que provee las velocidades de las ruedas.

## 1. Idea

La **odometría** consiste en estimar la pose del robot integrando incrementalmente las velocidades reportadas por sus encoders:

- Cada rueda tiene un encoder que mide su giro.
- Con la cinemática directa se traduce el par $(\dot{\phi}_L, \dot{\phi}_R)$ a $(v, \omega)$, y luego a la pose $\langle x, y, \theta\rangle$.
- Sin observaciones externas, sólo se confía en las velocidades comandadas/medidas.

![[Odometria - dead reckoning trayectoria.png]]
*Odometría sin corrección (izquierda: trayectoria real; derecha: deriva acumulada), slide 34.*

## 2. El error se acumula

Como cada paso integra ruido (deslizamiento, alfombra, baches, diámetros desiguales), la odometría **deriva** sin cota. La pose estimada se aleja monotónicamente de la real con el tiempo.

> [!warning] Limitación fundamental
> Por sí sola, la odometría no sirve para localización a largo plazo. Tiene que combinarse con observaciones del entorno — es justamente lo que hacen los filtros de Bayes del [[5. Filtros Bayesianos/Filtros Bayesianos|módulo 5]].

## 3. Del concepto al modelo probabilístico

La odometría como **señal cruda** vive en este módulo (locomoción/control). Cuando esa señal se usa como **control $u_t$** dentro de un filtro de Bayes, se necesita modelar su incertidumbre — eso es el [[Modelo de Movimiento (Odometría)]] (M4), que descompone $u$ en `(rot1, trans, rot2)` y caracteriza el ruido con cuatro parámetros $\alpha_i$.

## Conexiones
- [[Cinemática del Robot Diferencial]] — la base sobre la que se integra.
- [[Encoders Ópticos]] — el sensor que provee las velocidades de rueda.
- [[Modelo de Movimiento (Odometría)]] — el modelo probabilístico $p(x_t \mid x_{t-1}, u_t)$ que consume la odometría como control (M4).

## Fuentes
- `Raw/Diapositivas/Teoricas/03-locomocion-3.pdf`
  - slides 34–35 → 1. Idea, 2. El error se acumula
