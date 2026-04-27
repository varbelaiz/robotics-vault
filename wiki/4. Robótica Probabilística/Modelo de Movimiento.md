---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Modelo de Movimiento

> Cómo incorporar el resultado de una acción al belief del robot: $P(x_t \mid u_t, x_{t-1})$.

## Motivación

El mundo es dinámico debido a:

1. **Acciones del robot** (moverse, agarrar, soltar)
2. **Acciones de otros agentes** (personas, otros robots)
3. **Paso del tiempo** (cambios naturales del entorno)

Las acciones **nunca suceden con absoluta certeza**. A diferencia de las mediciones (que reducen incertidumbre), las acciones en general **incrementan** la incertidumbre.

## Modelo de transición

Para incorporar el resultado de una acción $u$ al belief actual:

$$P(x_t \mid u_t, x_{t-1})$$

Este término especifica la densidad de probabilidad según la cual, ejecutando $u$, el estado cambia de $x_{t-1}$ a $x_t$.

## Ejemplo: cerrar una puerta

Matriz de transición para $u = \text{"cerrar puerta"}$:

| Estado anterior \ Estado posterior | abierta | cerrada |
|---|---|---|
| **abierta** | 0.1 | 0.9 |
| **cerrada** | 0   | 1.0 |

Si la puerta está abierta, la acción "cerrar" tiene éxito el 90% de las veces.

## Integrando acciones al belief

### Caso continuo

$$\bar{Bel}(x_t) = \int P(x_t \mid u_t, x_{t-1}) Bel(x_{t-1}) \, dx_{t-1}$$

### Caso discreto

$$\bar{Bel}(x_t) = \sum_{x_{t-1}} P(x_t \mid u_t, x_{t-1}) Bel(x_{t-1})$$

Suposición de independencia: eliminamos $u$ en el segundo factor de la suma.

## Resultado

Después de la acción, la distribución de belief se "esparce": la incertidumbre crece porque el robot no tiene certeza absoluta de dónde quedó.

## Conexiones
- [[Modelo de Movimiento (Velocidad)]] — modelo cinemático basado en velocidad (M2)
- [[Odometría y Modelo de Movimiento (Odometría)]] — modelo basado en encoders (M2)
- [[Filtro de Bayes]] — el modelo de movimiento es uno de sus dos ingredientes
- [[5. Filtros Bayesianos/Filtros Bayesianos|Módulo 5]] — implementaciones concretas

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - págs. 23–25 → Motivación
  - págs. 24–25 → Modelo de transición
  - págs. 26–27 → Ejemplo: cerrar una puerta
  - pág. 28 → Integrando acciones al belief
  - págs. 29–30 → Resultado