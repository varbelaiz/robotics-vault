---
modulo: 3. Sensores y Modelos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[Sensores y Modelos|← Sensores y Modelos]] | [[Robotica|← Inicio]]

# Modelo de Detección de Landmarks

> Modelo de sensor cuando lo que se observa son **marcadores conocidos** (balizas, marcas visuales, postes), en lugar de distancias continuas. Se basa en triangulación.

## Prerequisitos
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — sensores que pueden detectar landmarks.
- [[Modelo de Sensor Basado en Haz]] o [[Modelo de Campo de Verosimilitud]] — alternativas para sensores de distancia.

## 1. Tipos de landmarks

**Otros modelos** de sensores de proximidad:
- **Macheo de mapas** (sonar, laser): generar pequeños mapas locales con los datos del sensor y machearlos contra un modelo global.
- **Características o features** (sonar, laser, visión): extraer features (puertas, pasillos, esquinas) de los datos.

**Marcadores (landmarks)**:
- **Balizas activas** — radio, GPS.
- **Marcadores pasivos** — visuales, retro-reflexivos.

El método más común es la **triangulación**.

El sensor provee:
- **distancia** al landmark, o
- **orientación** al landmark, o
- **distancia y orientación**.

## 2. Ejemplo: Sony Aibo

En la liga RoboCup con perros Sony Aibo, los **postes de colores** alrededor de la cancha funcionan como landmarks pasivos. El robot detecta su pose por triangulación con los postes visibles.

![[Landmarks - Sony Aibo.png]]
*Cancha de RoboCup con postes coloreados como landmarks; Sony Aibo, slide 29.*

## 3. Modelo probabilístico

Dado el landmark $i$ con posición conocida $(m_x(i), m_y(i))$ en el mapa, la pose actual $x = \langle x, y, \theta\rangle$ y una observación $z = \langle i, d, \alpha\rangle$ (índice del landmark, distancia medida, orientación medida), las **distancia y orientación esperadas** son:

$$\hat{d} = \sqrt{(m_x(i) - x)^2 + (m_y(i) - y)^2}$$
$$\hat{\alpha} = \text{atan2}(m_y(i) - y,\, m_x(i) - x) - \theta$$

La probabilidad de detección es el producto de dos términos independientes:

$$p_{det} = \text{prob}(\hat{d} - d, \varepsilon_d) \cdot \text{prob}(\hat{\alpha} - \alpha, \varepsilon_\alpha)$$

```
landmark_detection_model(z, x, m):
  z = ⟨i, d, α⟩, x = ⟨x, y, θ⟩
  d̂ = sqrt((m_x(i) - x)² + (m_y(i) - y)²)
  α̂ = atan2(m_y(i) - y, m_x(i) - x) - θ
  p_det = prob(d̂ - d, ε_d) · prob(α̂ - α, ε_α)
  return p_det
```

![[Landmarks - algoritmo.png]]
*Algoritmo `landmark_detection_model`, slide 30.*

## 4. Distribuciones $P(z \mid x, m)$

La forma de la distribución resultante depende de **qué provee el sensor**:

- **Sólo distancia**: anillo ancho alrededor del landmark.
- **Sólo orientación**: cuña angular desde el landmark.
- **Distancia + orientación**: localización casi única — pequeño parche en una posición específica.

![[Landmarks - distribuciones.png]]
*Distintas formas de $P(z \mid x, m)$ según qué provee el sensor (sólo distancia, sólo orientación, ambas), slide 31.*

> [!info] Combinando observaciones
> Con un único landmark visto, la pose queda subdeterminada. Con tres landmarks no colineales y mediciones de distancia, la pose se determina casi unívocamente por triangulación.

## Variantes y conexiones
- [[Modelo de Sensor Basado en Haz]] — alternativa para sensores de distancia continua.
- [[Modelo de Campo de Verosimilitud]] — alternativa eficiente para lidar/sonar.
- [[MCL - Filtro de Partículas]] — usa este modelo cuando hay landmarks (forward-ref, M5).
- [[EKF]] — feature-based EKF SLAM consume landmarks (forward-ref, M5).

## Fuentes
- `Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf`
  - slides 27–28 → 1. Tipos de landmarks
  - slide 29 → 2. Ejemplo: Sony Aibo
  - slide 30 → 3. Modelo probabilístico
  - slide 31 → 4. Distribuciones $P(z \mid x, m)$