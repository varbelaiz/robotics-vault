---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf
  - Raw/Libro/ProbabilisticRobotics.pdf
ultima_actualizacion: 2026-04-28
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Suposición de Markov

> El estado actual resume toda la historia pasada. Condición clave para la recursividad en filtros bayesianos.

## Definición

![[Markov - DBN.png]]
*Red bayesiana dinámica: la observación $z_t$ depende sólo del estado actual $x_t$, parte 2 slide 32.*

La **suposición de Markov** establece que la observación $z_t$ es independiente de todas las observaciones pasadas $z_1, \ldots, z_{t-1}$ y acciones pasadas $u_1, \ldots, u_{t-1}$, dado el estado actual $x_t$:

$$P(z_t \mid x_t, z_{1:t-1}, u_{1:t}) = P(z_t \mid x_t)$$

De forma similar, el estado futuro solo depende del estado actual y la acción actual:

$$P(x_t \mid x_{t-1}, x_{t-2}, \ldots, u_t, u_{t-1}, \ldots) = P(x_t \mid x_{t-1}, u_t)$$

## Suposiciones subyacentes

Para que la suposición de Markov sea válida se requiere:

1. **Mundo estático**: el entorno no cambia por sí solo entre pasos
2. **Ruido independiente**: el ruido en mediciones y acciones es independiente entre pasos de tiempo
3. **Modelo perfecto**: no hay errores de aproximación en los modelos de sensor y acción

> [!warning] Violaciones típicas en la práctica (Thrun et al., §2.4.4)
> El libro lista cuatro fuentes habituales por las que el supuesto se rompe:
> 1. **Dinámica no modelada** — personas que se mueven, puertas que se abren, cambios en el entorno no capturados en $x_t$.
> 2. **Inexactitudes en los modelos** — $p(z_t \mid x_t)$ y $p(x_t \mid u_t, x_{t-1})$ son aproximaciones; sus errores correlacionan a lo largo del tiempo y rompen la independencia condicional.
> 3. **Errores de aproximación del belief** — representar la posterior con grilla, gaussiana o partículas introduce error que se acumula.
> 4. **Variables del software de control** — un único "objetivo" en memoria del controlador influencia toda una secuencia de $u_t$, lo que efectivamente hace que controles consecutivos no sean independientes.

> [!info] Robustez práctica (Thrun et al., §2.4.4)
> A pesar de estas violaciones, los filtros bayesianos son **sorprendentemente robustos**. La heurística clave es definir $x_t$ de modo que las variables no modeladas tengan efectos *cuasi-aleatorios* sobre las observaciones: si los efectos no modelados se promedian a cero, Markov "casi" se cumple y el filtro funciona. Esto justifica la práctica común de usar estados *incompletos* (sólo pose, sin modelar peatones) en lugar del estado completo teórico.

## ¿Por qué importa?

Sin la suposición de Markov, el robot necesitaría recordar toda la historia de observaciones y acciones para estimar su estado actual. Con Markov, bastan:
- El **belief** actual $Bel(x_{t-1})$ (que resume toda la historia pasada)
- La **acción** $u_t$
- La **observación** $z_t$

Esto hace posible el **filtro recursivo**.

## Conexiones
- [[Filtro de Bayes]] — usa Markov para derivar el algoritmo recursivo
- [[Filtros Discretos]] — implementación en grilla con suposición Markoviana
- [[MCL - Filtro de Partículas]] — también asume Markov

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - pág. 21 → Primera aparición ("Suposición de Markov" como tag dentro de la actualización recursiva bayesiana)
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf`
  - pág. 32 → Definición formal (DBN + 2 ecuaciones) y suposiciones subyacentes
- `Raw/Libro/ProbabilisticRobotics.pdf`
  - pág. 17 → Estado completo (§2.3.1)
  - pág. 30 → Violaciones típicas + robustez práctica (§2.4.4)