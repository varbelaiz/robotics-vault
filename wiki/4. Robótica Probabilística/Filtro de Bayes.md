---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Filtro de Bayes

> Algoritmo recursivo para estimar el estado (belief) de un sistema dinámico dado observaciones y acciones.

## Problema

![[Filtro Bayes - estructura.png]]
*Estructura del filtro de Bayes: dato + sensor + acción + prior → belief, slide 31.*

**Dado**:
- Una serie de observaciones $z_1, \ldots, z_t$ y acciones $u_1, \ldots, u_t$
- Modelo de sensor: $P(z_t \mid x_t)$
- Modelo de acción (transición): $P(x_t \mid u_t, x_{t-1})$
- Probabilidad a priori: $P(x_0)$
Si **Se desea**:
- Estimar el estado $X$ del sistema en cada paso
- La probabilidad a posteriori (**belief**): $Bel(x_t) = P(x_t \mid z_{1:t}, u_{1:t})$

## Derivación del algoritmo recursivo

Partiendo de $Bel(x_t) = P(x_t \mid z_{1:t}, u_{1:t})$, se aplican sucesivamente: regla de Bayes (introducir $z_t$ como evidencia), suposición de Markov (eliminar dependencias pasadas), ley de probabilidad total (marginalizar sobre $x_{t-1}$), e independencia condicional. El resultado final es la forma recursiva.

![[Filtro Bayes - derivacion final.png]]
*Forma final de la derivación: aparece el término recursivo $Bel(x_{t-1})$, slide 40.*

El filtro tiene dos pasos:

### 1. Predicción (acción)

Cuando el robot ejecuta una acción $u_t$:

$$\bar{Bel}(x_t) = \int P(x_t \mid u_t, x_{t-1}) Bel(x_{t-1}) \, dx_{t-1}$$

La acción **aumenta** la incertidumbre (el robot no sabe exactamente dónde quedó).

### 2. Actualización (percepción)

Cuando el robot toma una medición $z_t$:

$$Bel(x_t) = \eta P(z_t \mid x_t) \bar{Bel}(x_t)$$

La percepción **reduce** la incertidumbre.

## Algoritmo

![[Filtro Bayes - algoritmo.png]]
*Pseudocódigo del filtro de Bayes, slide 41.*

```
Filtro_de_Bayes(Bel(x), d):
    η = 0
    Si d es una medición z:
        Para cada x:
            Bel'(x) = P(z | x) * Bel(x)
            η = η + Bel'(x)
        Para cada x:
            Bel'(x) = η * Bel'(x)
    Si d es una acción u:
        Para cada x:
            Bel'(x) = Σ_x' P(x | u, x') * Bel(x')
    Retornar Bel'(x)
```

## Aplicación: localización probabilística

![[Localizacion probabilistica - pasos.png]]
*El filtro de Bayes aplicado a localización: el belief sobre la posición del robot evoluciona con cada acción y cada medición, slide 43.*

La localización es la aplicación canónica del filtro de Bayes: el estado $x_t$ es la pose del robot, $u_t$ son los comandos de movimiento (o la odometría), y $z_t$ son las mediciones del lidar/sonar/cámara. El belief inicial puede ser uniforme (localización global) o concentrado (tracking).

## Usos del filtro de Bayes

El filtro de Bayes es la base de muchas herramientas:

- **Filtros de Kalman** — sistemas lineales gaussianos
- **Filtros de Partículas** (MCL) — representación con muestras
- **Modelos Ocultos de Markov** — estados no observables directamente
- **Redes Bayesianas Dinámicas** — modelos gráficos temporales
- **POMDPs** — procesos de decisión bajo incertidumbre

## Resumen

- La regla de Bayes permite calcular probabilidades que de otra forma serían complejas
- Con la suposición de Markov, la actualización recursiva es eficiente
- Los filtros de Bayes estiman el estado de un sistema dinámico de forma recursiva

## Conexiones
- [[Regla de Bayes]] — base matemática
- [[Suposición de Markov]] — condición de independencia temporal
- [[Modelo de Movimiento]] — $P(x_t \mid u_t, x_{t-1})$
- [[Modelo de Sensor]] — $P(z_t \mid x_t)$
- [[Filtros Discretos]] — primera implementación práctica
- [[Filtro de Kalman]] — versión para sistemas lineales gaussianos
- [[MCL - Filtro de Partículas]] — versión con representación por muestras

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf`
  - slide 31 → Problema
  - slides 33–40 → Derivación del algoritmo recursivo
  - slide 41 → Algoritmo
  - slide 42 → Usos del filtro de Bayes
  - slide 43 → Aplicación: localización probabilística
  - slide 45 → Resumen