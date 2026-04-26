# Catálogo del Wiki

> Lista plana de todas las páginas del wiki, agrupadas por módulo.
> Actualizado en cada ingesta. Una línea por página: `[[link]] — descripción corta.`

> [[Home|← Inicio]]

---

## 1. Fundamentos
- [[1. Fundamentos/_Overview|_Overview]] — Mapa del módulo: vectores, matrices, rotaciones, transformaciones.
- [[Vectores]] — Arreglos ordenados de números; producto interno, dependencia lineal.
- [[Matrices]] — Operaciones, productos, rango, inversa, traza, determinante.
- [[Matriz Definida Positiva]] — Análoga al "número positivo"; covarianzas en filtros.
- [[Matriz Jacobiana]] — Generalización del gradiente; linealización en EKF.
- [[Rotaciones]] — Matrices ortogonales; rotaciones 2D y 3D, no conmutatividad.
- [[Transformaciones Homogéneas]] — Rototraslación; pose del robot; composición.

## 2. Locomoción
- [[2. Locomoción/_Overview|_Overview]] — Mapa del módulo: cinemática, accionamientos, odometría y modelos de movimiento.
- [[Locomoción y Tipos de Accionamientos]] — Tipos de locomoción, ruedas, accionamientos canónicos y restricciones holonómicas.
- [[Cinemática del Robot Diferencial]] — Pose 2D, derivación completa de la cinemática directa con ICC.
- [[Otros Accionamientos - Ackermann, Síncrono, Omni]] — Ackermann, síncrono, mecanum, vehículos especiales.
- [[Odometría y Modelo de Movimiento (Odometría)]] — Dead reckoning + modelo probabilístico basado en encoders.
- [[Modelo de Movimiento (Velocidad)]] — Modelo probabilístico cuando el control viene como $(v, \omega)$.

## 3. Sensores y Modelos
- [[3. Sensores y Modelos/_Overview|_Overview]] — Mapa del módulo: hardware sensorial + modelos probabilísticos $p(z\|x,m)$.
- [[Sensores Internos]] — Clasificación, encoders, táctiles, magnetómetro, giróscopo, acelerómetro, IMU.
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — GPS/RTK, sonar, lidar, RGBd, estéreo.
- [[Modelo de Sensor Basado en Haz]] — Mezcla de hit/unexp/max/rand para sonar y lidar.
- [[Modelo de Campo de Verosimilitud]] — Alternativa eficiente y suave al beam-based.
- [[Modelo de Detección de Landmarks]] — Modelo cuando se observan marcadores conocidos.

## 4. Robótica Probabilística
- [[4. Robótica Probabilística/_Overview|_Overview]] — Mapa del módulo: probabilidad → Bayes → filtro de Bayes.
- [[Probabilidad - Axiomas y Variables Aleatorias]] — Axiomas, variables discretas y continuas, conjunta, condicional, marginalización.
- [[Regla de Bayes]] — Fórmula, normalización, ejemplo de la puerta (causal vs diagnóstico).
- [[Actualización Recursiva Bayesiana]] — Cómo combinar múltiples observaciones con la suposición de Markov.
- [[Modelado de Acciones]] — $P(x \mid u, x')$ y por qué las acciones aumentan la incerteza.
- [[Filtro de Bayes]] — Derivación, algoritmo y ejemplo de localización probabilística.

## 5. Filtros Bayesianos
- [[5. Filtros Bayesianos/_Overview|_Overview]] — *(módulo en esqueleto, pendiente de ingesta)*

## 6. Mapeo
- [[6. Mapeo/_Overview|_Overview]] — *(módulo en esqueleto, pendiente de ingesta)*

## 7. ROS2 y TPs
- [[7. ROS2 y TPs/_Overview|_Overview]] — *(módulo en esqueleto, pendiente de ingesta)*
